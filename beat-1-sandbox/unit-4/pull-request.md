# Unit 4 — Test and Submit

Path: `beat-1-sandbox/unit-4/pull-request.md`

Record of the pull request you opened against the Path Review repo, and of the evaluation
runs that produced `eval-run.txt`. This file is graded at the path above; a copy kept
anywhere else in the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the wrong
label is not graded.

---

## Your pull request

**Pull request**

https://github.com/codepath/pathreview-ai301-fa26-s3/pull/195

**Branch**

`fix/9-review-content-hash-cache` (closes #9)

## Eval iterations

Answer all four sections. Quote source text directly; paraphrase does not satisfy these
fields.

**Run history**

First run: 16/20

Final run (the one saved to `eval-run.txt`): `agreement: 19/20 scored items  (bar: 18/20: PASS)`

**Package analysis**

**pkg-11** (category `clear-accept`). My rubric decided **reject**; the gold label is **accept**.
The eval run records the deciding check: `pkg-11  clear-accept  accept  reject   NO     failed: reported-changes-in-diff`.

Why: `reported-changes-in-diff` fails on "a single reported change the diff does not contain  
or contradicts". My rubric was too strict about naming every single change in the diff.

**Check rationale**

> | how-tested | The test-evidence section read against the plan's test plan and the issue's repro. | (a) The evidence re-runs the issue's repro (the plan's primary failing case) on the branch and shows an observable before AND after (output, exit code, rendered result) on the changed code path; AND (b) every other case the test plan names (controls, secondary cases) is reported with its outcome. A stated outcome ("controls unchanged", "same-key redefine prints the warning") is enough for these; output is not required. Fails on "tests pass", "works on my machine", or "verified locally" with no observable repro before/after; on evidence that runs only a control or an unchanged path instead of the repro; or on any test-plan case the evidence never mentions. | required |



**Trade-offs**

`reported-changes-in-diff` costs a clear accept. In the final run it was the only check
that disagreed with gold, flipping **pkg-11** (gold `accept`) to `reject`. Every other
category scored full marks (`not-tested 4/4  silent-drift 4/4  standards-wall 2/2
unreviewable 3/3`), and the silent-drift packages depend on this check catching claims
the diff doesn't back up. I accept that a strict reading will sometimes hold an honest PR
whose claim the grader can't match to a hunk. Loosening it risks letting silent-drift
packages through, and the run still clears the bar at 19/20 (bar 18/20).



---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/pr-precheck/`.
