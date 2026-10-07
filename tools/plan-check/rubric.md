# Rubric: is this plan ready to post and build from?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here (via your procedure.md). It ships empty on
purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the plan's scope statement, the test plan read against
     the repro evidence's steps, the plan comment read against the
     thread highlights, the repo-facts block) or a location from your
     references/evidence-guide.md. "The plan" is not a source; "the
     plan's stated cause read against what the repro evidence shows"
     is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (is
     this one bounded change? could a stranger start executing it?),
     never the write-up's shape (how many sections it has, how long it
     is, whether it uses headings). Structure-shaped checks are what
     make graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad plans posted. The lecture named the
failure families: the diagnosis ignores or contradicts the reproduced
evidence, the change is unbounded (scope creep), the plan targets the
symptom while the evidence points at the cause, a stranger could not
start executing it, the test plan proves nothing observable, the
unknowns are dressed up as certainty, and the comment ignores what the
thread or the repo's stated conventions ask. A rubric that ignores a
family will fail eval packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| diagnosis-grounded | The candidate plan's stated cause, read against the repro-evidence block's steps and Actual/Expected lines (and the issue body where the repro evidence is silent). | Pass if the stated cause explains the specific behavior the repro evidence shows (the step where actual diverges from expected), contradicts none of the observed facts, and names a mechanism upstream of the symptom rather than restating or suppressing the symptom itself. Fail if the cause ignores or contradicts what the repro observed, or if the change only masks the symptom (hides the output, adds a workaround, special-cases the repro input) while the evidence points at a deeper cause. Fail if no cause is stated. | required |
| scope-bounded | The candidate plan's change / in-scope statement and its out-of-scope line, read against the cause it states; the plan comment's description of the change. | Pass if the plan describes one change that addresses the stated cause, names where it lands (file, function, or component), and everything it touches is needed to fix this issue. Fail if it bundles unrelated work (refactors, renames, new features, fixes to other issues, "while I'm in there" cleanups), touches areas the stated cause does not implicate, or gives no boundary at all, so a reviewer could not say what would be out of scope. | required |
| test-plan-decisive | The candidate plan's test section, read against the repro-evidence block's numbered steps and its Actual line. | Pass if the test plan re-runs the reproduction (or an equivalent automated test of the same behavior) and names the observable result at the failing step that must change, so that the test would fail before the fix and pass after it. Fail if it names no observable outcome ("verify it works", "run the tests", "check nothing broke"), tests something other than the reproduced behavior, or could pass with the bug still present. | required |
| executable | The candidate plan's change paragraph (files, functions, approach), read against the stated cause. | Pass if a stranger could start the in-scope work without asking the author anything: the plan names where the change lands (a file, function, or component) and commits to one approach for it. Explicitly deferring separate, out-of-scope work (with the deferral stated) is fine. Fail if the in-scope change itself is left open: no location named ("somewhere", "the input stack"), alternatives left for build time ("upstream or vendored, whichever is easier", "gocui? tcell? not sure"), or an activity in place of a change ("profile and optimize", "investigate"). | required |
| comms-thread-and-policy | The candidate plan comment, read against the thread highlights (maintainer or owner comments) and the repo-facts block's contribution policy line. | Pass if the comment (a) engages any explicit direction a maintainer or owner gave in the thread (a pointed-at culprit, a requested test, a settled approach, a linked PR), either following it or saying why it departs, and (b) meets every requirement the stated contribution policy places on comments or PRs. Treat every package as AI-assisted work: if the policy requires AI-use disclosure, the comment must contain one. Fail if it ignores explicit maintainer direction or omits a required disclosure. Pass when the thread gives no direction and the policy states no requirement. Tone, length, and announcing a PR are not graded here. | required |

## Verdict rule

Each check gets exactly one grade: `pass`, `fail`, or `unclear`.

**Accept** only if every `required` check grades `pass`. Any `fail`
or `unclear` on a `required` check means **reject**. `preferred`
checks are reported but never change the verdict.

**When to grade `unclear` (and when not to):**

- `unclear` is for when the package does not contain enough evidence
  to decide, through no fault of the plan: for example, the repro
  evidence is silent on the step the diagnosis depends on, or the
  issue and the repro evidence describe the behavior differently and
  the package does not settle which one is right.
- A file or function name you cannot check against source is not by
  itself `unclear`: the bundle carries no source code. Judge whether
  the named location fits the stated cause and the observed behavior.
- If the plan itself leaves something out that the check asks for (no
  stated cause, no boundary, no observable test outcome), grade
  `fail`, not `unclear`. Missing content is the plan's failure, not
  ambiguity in the evidence.
- If the evidence leans clearly one way, grade that way. Do not use
  `unclear` to avoid a judgment call that the pass condition already
  decides.

**How `unclear` enters the verdict:** an `unclear` on a `required`
check counts as `fail`, so the package is held. A plan whose
required checks cannot be verified from the package is
not ready to post and build from. The `evidence` line for an
`unclear` check must name the specific missing fact, so the author
knows what to add to make it gradable.

**What gets quoted:** for a reject, the output's deciding evidence is
the first `required` check (in table order) that failed or was
unclear, quoted from the package.
