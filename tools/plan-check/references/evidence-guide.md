# Evidence guide: where evidence lives in a plan package

<!--
THIS IS THE PART YOU WRITE (second week running: the judgment files
stay in your hands). The skill uses this guide as its map: for every
kind of evidence a rubric check names, this file says WHERE to find it
in a plan package and WHAT GOOD LOOKS LIKE when you do.

Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the issue context, the repro-evidence block,
  the candidate plan's scope statement or test plan, the plan comment,
  the repo-facts block). In live mode (where on GitHub or in the
  draft: the issue thread, the student's posted repro comment, the
  repo's docs, the draft plan and comment).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("the stated cause cites behavior the
  repro evidence actually shows") over adjectives ("diagnosis is
  solid").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute, and this week that cuts twice: your
procedure.md tells the skill WHEN to gather each family, and this
guide tells it WHERE. Write the map you wish your executor had.
-->

Worked example throughout: `eval/packages/calib-01.md` (lazygit#5900,
commit color not refreshing after a push).

## Diagnosis and grounding

- **Where it lives (eval):** the `Cause:` line of `## Candidate plan`,
  read against `## Repro evidence`: the numbered steps, `Expected:`
  and `Actual:`. Use `## Issue` only where the repro evidence is
  silent.
- **Where it lives (live):** the cause in the draft `plan.md`, read
  against the student's posted repro comment on the issue.
- **calib-01:** the cause is "after a push started from the
  branch-commits view, that view's model is not refreshed, so the
  commits keep their pre-push push-status flags until the view is
  rebuilt on re-entry". The repro shows that the push succeeded (step
  2, confirmed with `git log origin/main -1`), the color stayed stale
  (step 3), and re-entering fixed it (step 4).
- **What good looks like:** the cause accounts for the step where the
  actual result diverges from the expected one, and for every other
  observed fact. In calib-01, step 4 (re-entry fixes it) rules out
  push status being computed wrong and points at a stale view. The
  cause says exactly that. A cause that blamed the push itself would
  contradict step 2.

## Scope

- **Where it lives (eval):** the `Change:` paragraph of
  `## Candidate plan`, including its `In:` and `Out:` lines. Check it
  against how `## Candidate plan comment` describes the change.
- **Where it lives (live):** the change and in/out-of-scope lines in
  the draft `plan.md`, plus the draft comment.
- **calib-01:** In: "the push completion callback in
  `pkg/gui/controllers/sync_controller.go` adds the commits context to
  its post-push refresh scope." Out: "any change to how push status is
  computed, or to other views' refresh behavior." The comment calls it
  "a one-change fix in the sync controller's push callback."
- **What good looks like:** one change, at a named location, that the
  stated cause directly implicates, plus an explicit line saying what
  will not be touched. A drive-by rewrite either names areas the cause
  never mentions or has no `Out:` boundary at all.

## Executability

- **Where it lives (eval):** the `Change:` paragraph of
  `## Candidate plan`: the named file, function or callback, and what
  is done there.
- **Where it lives (live):** the approach section of the draft
  `plan.md`.
- **calib-01:** names the file (`sync_controller.go`), the hook (the
  push completion callback) and the action (add the commits context to
  the refresh scope).
- **What good looks like:** a stranger knows where to open the code
  and what edit to make first, without asking the author anything.
  "Fix the refresh logic" fails this test.

## Test plan

- **Where it lives (eval):** the `Test:` line of `## Candidate plan`,
  mapped onto the numbered steps in `## Repro evidence`.
- **Where it lives (live):** the test section of the draft `plan.md`,
  mapped onto the posted repro comment's steps.
- **calib-01:** "repro steps above; at step 3 the color must flip
  without leaving the view." It also covers the main commits panel
  and force push, "since both share the callback."
- **What good looks like:** it names the repro step and the
  observable result that must change (step 3: stale yellow becomes
  the pushed color), so it fails before the fix and passes after.
  Extra cases are a plus when they come from the change's reach (here,
  the shared callback). "Verify the color updates" without a step or
  an outcome is vague.

## Honesty

- **Where it lives (eval):** confident claims in the `Cause:` line and
  in `## Candidate plan comment`, compared with what the repro evidence
  actually established. Also check any risks or unknowns the plan
  states.
- **Where it lives (live):** the same places, plus a deviation note in
  `plan.md` if the build diverged from the posted plan.
- **calib-01:** the cause is a mechanism the repro is consistent with
  but did not observe directly (no code was read in the repro). The
  comment states it flatly: "just misses the branch-commits context."
  The plan lists no unknowns.
- **What good looks like:** claims the repro proved are stated as
  facts. Inferred mechanisms are either backed by a named code
  location or flagged as the working hypothesis. False confidence
  states an unverified cause as settled and lists no risks where
  there plainly are some.

## Comms

- **Where it lives (eval):** `## Candidate plan comment`, read against
  `## Thread highlights` (maintainer asks or direction already given)
  and `## Repo facts`: the bug-report template asks and the
  `contribution policy` line, including any AI disclosure
  requirement.
- **Where it lives (live):** the draft comment, read against the live
  issue thread and the repo's CONTRIBUTING / templates.
- **calib-01:**
  - The thread has "(no comments)", so there is no maintainer
    direction to follow.
  - The repo facts say the maintainer "reviews outside pull requests
    only selectively", and AI-generated PRs are hard to assess. There
    is "no stated AI disclosure requirement".
  - The comment says it is "keeping it minimal given the
    review-bandwidth note in CONTRIBUTING", which speaks to the
    policy. It also says "will send the PR shortly", announcing a PR
    to a maintainer who reviews outside PRs only selectively, without
    asking first.
  - The comment cites the version reproduced (0.64.1), which matches
    the template's version ask.
- **What good looks like:** the comment answers what the thread and
  the repo actually ask (a requested direction, a required disclosure,
  a stated PR policy) in its own words. Boilerplate reads the same on
  any issue and ignores those signals.
