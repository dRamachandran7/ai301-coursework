# Unit 3 — Plan and Build

Path: `beat-1-sandbox/unit-3/plan-and-implement.md`

Record of your plan, the branch you built it on, and the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in the
repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Posted upstream

**GitHub username**

dRamachandran7

**Plan comment**

[https://github.com/codepath/pathreview-ai301-fa26-s3/issues/9#issuecomment-6028914745](https://github.com/codepath/pathreview-ai301-fa26-s3/issues/9#issuecomment-6028914745)

Here's my plan for #9, based on the reproduction I posted above (two POST /reviews calls for an unchanged profile returned two review ids and ran every stage twice: {'ingestion': 2, 'agent': 2, 'rag': 2}). The cause is that create_review_endpoint in api/routes/reviews.py always creates a new pending review and schedules process_review, and Review stores nothing about the profile content it was generated from, so there is nothing to compare against. I'll add a nullable content_hash column to Review (migration 003), have process_review write a SHA-256 of github_username, portfolio_url, resume_text, and resume_filename when a review completes, and add a get_or_create_review helper in core/services/review_service.py that returns the latest completed review for the same profile and hash; the route will only schedule the pipeline on a miss. Editing any of those fields changes the hash, so the next submission is a miss, while legacy, pending, and failed reviews never match. To test, I'll re-run my repro expecting one review id and one run per stage, and add unit tests in tests/unit/test_review_service.py for the hash, cache hit, cache miss, hash written on completion, and the route skipping the background task on a hit. Out of scope: rag/generator/review_generator.py (nothing calls ReviewGenerator yet), Redis, the #65 xfail tests, and de-duplicating submissions that arrive while the first is still processing. One known limitation is that the hash covers profile fields rather than the content at those sources, so an updated GitHub or portfolio site without a profile edit would still return the old review; I'll note that in the PR. If maintainers would prefer a new review row per submission instead of returning the existing one, that only changes the lookup step.

---

## Your branch

**Branch**

`fix/9-review-content-hash-cache`, pushed to my fork
`dRamachandran7/pathreview-ai301-fa26-s3`, one commit: `c41c030` "implemented cache
system".

**Evidence**

The Unit 2 reproduction re-run against the branch. Same script as the reproduction
comment on #9, with one change to the in-memory stand-in session: its `execute` now
answers a `Review` query filtered by `profile_id` + `content_hash` + `status`, not only
a lookup by review id. The pre-fix code never issues that query, so the before output is
unaffected; without that change the stand-in returns `None` for the cache lookup and
reports a miss whatever the fix does. The counted stage wrappers, the real route, the
real `get_or_create_review` and the real `process_review` are untouched.

```python
    async def execute(self, stmt):
        entity = stmt.column_descriptions[0]["entity"]
        if entity is Profile:
            return SimpleNamespace(
                scalars=lambda: SimpleNamespace(first=lambda: self.profile)
            )

        params = {
            name.rsplit("_", 1)[0]: value
            for name, value in stmt.compile().params.items()
        }

        if "content_hash" in params:
            # The cache lookup: newest complete review for this content.
            matches = [
                rv
                for rv in self.reviews.values()
                if str(rv.profile_id) == str(params["profile_id"])
                and rv.content_hash == params["content_hash"]
                and rv.status == params["status"]
            ]
            matches.sort(key=lambda rv: rv.updated_at, reverse=True)
            row = matches[0] if matches else None
        else:
            row = self.reviews.get(str(params["id"]))

        return SimpleNamespace(scalars=lambda: SimpleNamespace(first=lambda: row))
```

Before — `main` at `2f4e82f`, run in a second worktree of the fork:

```text
$ PYTHONPATH=. .venv/bin/python /path/to/repro_issue9.py
POST /reviews #1: HTTP 200, id=294a1f44-4662-4c88-a799-76ac1f3fc240, status=pending
POST /reviews #2: HTTP 200, id=0124804c-d364-4a43-882b-6aceb6a718a0, status=pending
distinct review ids: 2
  294a1f44-4662-4c88-a799-76ac1f3fc240: status=complete, overall_score=0.81
  0124804c-d364-4a43-882b-6aceb6a718a0: status=complete, overall_score=0.81
pipeline stage runs: {'ingestion': 2, 'agent': 2, 'rag': 2}
```

Two distinct review ids, and every stage ran twice: the reproduced failure.

After — `fix/9-review-content-hash-cache` at `c41c030`:

```text
$ PYTHONPATH=. .venv/bin/python /path/to/repro_issue9.py
POST /reviews #1: HTTP 200, id=7e2895e3-bf90-46c0-b689-efa77790e053, status=pending
POST /reviews #2: HTTP 200, id=7e2895e3-bf90-46c0-b689-efa77790e053, status=complete
distinct review ids: 1
  7e2895e3-bf90-46c0-b689-efa77790e053: status=complete, overall_score=0.81
  7e2895e3-bf90-46c0-b689-efa77790e053: status=complete, overall_score=0.81
pipeline stage runs: {'ingestion': 1, 'agent': 1, 'rag': 1}
```

One review id across both submissions, the second returning `status=complete` rather
than a fresh `pending`, and one run per stage: the result the plan said to expect. The
run also logs the new `review_cache_hit` line on the second call, with the first call's
`review_id`:

```text
2026-10-06 22:44:50 [info     ] review_cache_hit               profile_id=38c0f2fc-618b-4d22-b216-4e4948f3a886 request_id=7c3c5fc9-e96d-4c48-aabd-8cfef75ad27f review_id=90c5109c-1ff8-42cc-9749-4d815067eca4 user_id=c2ab95a9-3b35-4dcd-8f1d-fdb4b6c576c3
```

The unit tests on the branch pass as well:

```text
$ .venv/bin/python -m pytest tests/unit/test_review_service.py -q
19 passed, 13 xfailed, 9 warnings in 0.97s
```

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

20/20.

This was the run that produced the committed `eval-run.txt`, whose last line reads
`agreement: 20/20 scored items  (bar: 18/20: PASS)`.

**Package analysis**

`pkg-10` (source: `starship/starship#7677`), category `unbuildable`.

Gold label: `reject`. My rubric: `reject`. From the committed `eval-run.txt`:

```text
pkg-10  unbuildable        reject  reject   yes    
```

My rubric read it that way on `executable`, which asks whether a stranger could start
the in-scope work without asking the author anything. The candidate plan's four steps
are activities, not a change. Step 1 is "Profile starship on Windows to find the slow
parts of the git modules", step 2 "Investigate whether scoop-installed git behaves
differently from the official installer", step 3 "Look into caching git information
between prompts", and step 4 "Optimize whatever the profiling turns up, and consider
spawning fewer git subprocesses in general". No file, function or component is named
anywhere, and step 4 defers the actual approach to whatever profiling later reveals,
which is the "an activity in place of a change ('profile and optimize',
'investigate')" clause in my pass condition.

`test-plan-decisive` fails too, and either one alone is enough to hold the package,
since both are `required`. The plan's test plan reads "after optimizing, the prompt
should feel fast in big repos on Windows, and `starship timings` should look much
better". "Should feel fast" and "should look much better" name no observable result at
a failing step, so the test could pass with the bug still present.

**Check rationale**

Quoted from `tools/plan-check/rubric.md` exactly as it reads now:

```markdown
| test-plan-decisive | The candidate plan's test section, read against the repro-evidence block's numbered steps and its Actual line. | Pass if the test plan re-runs the reproduction (or an equivalent automated test of the same behavior) and names the observable result at the failing step that must change, so that the test would fail before the fix and pass after it. Fail if it names no observable outcome ("verify it works", "run the tests", "check nothing broke"), tests something other than the reproduced behavior, or could pass with the bug still present. | required |
```

It reads that way because the thing worth grading is whether the test can tell the two
worlds apart, not whether the plan has a test section. I rejected the structure-shaped
version of this check — "the plan states how it will be tested" — because `pkg-10`
passes that version: it does state how it will be tested, in the words "the prompt
should feel fast". Naming the artifact to compare against (the repro evidence's
numbered steps and its Actual line) and the property to decide on (fails before the
fix, passes after) is what separates that from `pkg-02`'s test plan. The explicit list
of rejected phrasings is there because those are the exact forms the vague packages
use, and spelling them out stops the check drifting between runs.

My own work is what convinced me the check needs to bite this hard. My first "after"
run of the reproduction above printed the same two ids and
`{'ingestion': 2, 'agent': 2, 'rag': 2}` as the "before" run. I had written down the
expected result (one id, one run per stage), so the contradiction was visible, and the
cause turned out to be the stand-in session rather than the fix. A test plan that only
said "re-run my repro and check it works" would have hidden that.

**Trade-offs**

The check gives up on issues that have no reproduction to re-run. A plan for a feature
request, or for a bug whose repro evidence is a single screenshot with no steps, cannot
name "the observable result at the failing step", because the package has no failing
step to point at. My rubric holds those packages even when the plan is sound, and I
accept that: every package in this eval set carries a repro-evidence block, so the
case does not appear in the 20 scored items, and inside the Path Review house issues a
reproduction is always available. If the skill were pointed at the wider GitHub later
in the course, this is the first check I would revisit, and the repair would be a
second pass condition for packages whose evidence block has no numbered steps, not a
loosening of this one.

---

Related paths: `plan.md` and `eval-run.txt` in this directory; your skill's files in
`tools/plan-check/`.
