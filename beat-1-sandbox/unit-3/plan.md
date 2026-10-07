# Plan: #9 — cache reviews for unchanged portfolio submissions

Issue: https://github.com/codepath/pathreview-ai301-fa26-s3/issues/9
Repro: my comment on #9 (2026-09-29), `main` at `2f4e82f`
Branch: `fix/9-review-content-hash-cache` (on my fork)

## What the repro showed

Two `POST /reviews` calls with the same `profile_id` and no profile change in
between returned two different review ids (both `pending`), both reviews ended
`complete` with `overall_score=0.81`, and every pipeline stage ran twice:
`{'ingestion': 2, 'agent': 2, 'rag': 2}`. Expected: the second call returns the
stored review, and the pipeline runs once in total.

## Cause

Nothing in the review path checks for an earlier result. In
`api/routes/reviews.py`, `create_review_endpoint` always calls
`create_review` (`core/services/review_service.py`), which inserts a new
`pending` row, and then always schedules `process_review`. Neither function
loads the profile's content or compares it to an earlier review. `Review` has
no column that records which profile content a review was generated from, so
there is nothing to compare against. (`IngestedSource.content_hash` exists but
is per-source, is never written, and isn't read by the review path.)

## Change

One change: record a content hash on each completed review, and check it
before creating a new review.

1. **Hash function** in `core/services/review_service.py`:
   `compute_profile_content_hash(profile) -> str`. SHA-256 hex of
   `json.dumps({...}, sort_keys=True)` over the four profile fields the
   pipeline reads: `github_username`, `portfolio_url`, `resume_text`,
   `resume_filename`. These are exactly the fields `_run_ingestion_pipeline`
   uses. `None` is kept as `null`, so a missing field and an empty string hash
   differently.
2. **Column**: add `content_hash: Mapped[str | None]` (`String(64)`, nullable)
   to `Review` in `core/models/review.py`, plus an index on
   `(profile_id, content_hash)`. New migration
   `alembic/versions/003_add_content_hash_to_reviews.py`, written the same way
   as `002` (`add_column` + `create_index`, and the reverse in `downgrade`).
   Existing rows stay `NULL`, so they never match.
3. **Write the hash on completion**: in `process_review`, at step 6 (the same
   place `status="complete"` is set), set
   `review.content_hash = compute_profile_content_hash(profile)` from the
   profile the run actually used. Failed and safety-rejected reviews never get
   a hash.
4. **Look up before creating**: new service function
   `get_or_create_review(db, profile_id, user_id) -> tuple[Review, bool]`.
   It loads the profile, computes the hash, and selects the most recent
   `Review` where `profile_id` matches, `content_hash` matches, and
   `status == "complete"` (ordered by `updated_at` desc). On a hit it returns
   `(cached_review, True)`. On a miss, or if the profile isn't found, it calls
   the existing `create_review` and returns `(new_review, False)`.
5. **Route**: `create_review_endpoint` calls `get_or_create_review` instead
   of `create_review`, and only calls `background_tasks.add_task(process_review, ...)`
   when the second value is `False`. On a hit it logs `review_cache_hit` with
   the review id, and returns the cached review in the same
   `ReviewResponse` shape (status `complete`). The response schema doesn't
   change.

**Invalidation** comes from the key itself, so there's no separate expiry.
Editing any of the four fields through `PUT /profiles/{id}` changes the hash,
and the next submission misses.

**In scope:** `core/services/review_service.py`, `core/models/review.py`, the
new `003` migration, `api/routes/reviews.py` (the scheduling branch only), and
new tests in `tests/unit/test_review_service.py`.

**Out of scope:**
- `rag/generator/review_generator.py`. The issue lists it, but nothing calls
  `ReviewGenerator` yet (`process_review` uses the placeholder `_run_*`
  helpers), so caching there would not affect the reproduced behavior.
- Redis or any in-memory cache. The stored review in Postgres already is the
  cached value.
- Fixing the existing `xfail` tests in `test_review_service.py` (that's #65).
- Using `IngestedSource.content_hash`, or any other ingestion changes.
- Deduplicating two identical submissions that arrive while the first one is
  still `pending`/`processing`. Both will still run, the same as today.

## Test plan

**Re-run the repro.** Same script and steps as my comment on #9, with one
change: the `FakeSession.execute` stand-in has to answer the new cache lookup
(a `Review` select filtered on `profile_id` + `content_hash` + `status`). In
the current script that query would fall into the review-by-id branch and
always miss. Required output after the fix:
- `distinct review ids: 1`. The second `POST /reviews` returns the first
  review's id with `status=complete`. (Before the fix: 2 ids, second one
  `pending`.)
- `pipeline stage runs: {'ingestion': 1, 'agent': 1, 'rag': 1}`. (Before the
  fix: 2 each.)

**New unit tests** in `tests/unit/test_review_service.py`, marked
`@pytest.mark.unit`. Each one fails on `main` because the function or column
doesn't exist there.
1. `compute_profile_content_hash` gives the same value for two profiles with
   identical fields, and a different value when any one of the four fields
   changes.
2. `get_or_create_review` returns `(existing, True)` and does not call
   `db.add` when the lookup finds a complete review with the matching hash.
3. `get_or_create_review` creates a new `pending` review (`cached=False`) when
   the lookup returns nothing. This covers changed content, legacy `NULL`-hash
   rows, and pending/failed reviews, since the query filters all of them out.
4. `process_review` sets `review.content_hash` when the review completes, and
   leaves it `None` when safety checks fail.
5. Route-level (with `TestClient` and dependency overrides, same setup as the
   repro): a cache hit does not schedule `process_review`, and a miss does.

**Checks:** `make check && make test-unit`, with all five CI jobs green.

## Risks and unknowns

- **Source changes the hash can't see.** The hash covers the profile's
  fields, not what's at those sources. If someone pushes new GitHub repos or
  updates their portfolio site without editing the profile, they get the old
  review. Ingestion is a placeholder today, so this doesn't affect current
  behavior. Once real ingestion lands, the key may need ingested content or a
  TTL. I'll flag this in the PR rather than solve it here.
- **Hit response shape.** On a hit I plan to return the existing review (same
  id, `complete`) rather than create a new row that copies it. That matches
  the issue ("returns the stored review") and keeps the review list free of
  duplicates. If a maintainer would rather have a fresh row per submission,
  only step 4 changes.
- **No forced re-review.** With this change, a user can't ask for a fresh
  review of unchanged content. I'm not adding a `force` flag unless someone
  asks for one.
- **Ownership.** `create_review` doesn't check that the profile belongs to
  `current_user` today. The lookup is scoped to `profile_id`, so a hit can't
  return another profile's review. I'm not adding an ownership check here,
  because it's a separate issue.
