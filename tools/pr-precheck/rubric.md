# Rubric: is this pull request ready to submit?

<!--
THIS IS THE PART YOU WRITE (fourth week running; this is the rubric's
final form in the sandbox). Your frame in SKILL.md executes whatever
checks you define here, via your procedure.md. It ships empty on
purpose: the judgment is your work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the diff read against the plan's scope, the test
     evidence read against the plan's test plan, the description read
     against the diff, the repo-facts block's template asks) or a
     location from your references/evidence-guide.md. "The PR" is not
     a source; "the diff's changed files read against the plan's
     stated boundary" is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself
     (does the diff fall inside the plan plus its deviation notes? is
     the claimed evidence observable?), never the write-up's shape
     (how long the description is, how many commits there are).
     Structure-shaped checks are what make graders disagree with
     themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (submit) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. State the `unclear`
   treatment explicitly: the frame here is YOUR SKILL.md, so a rubric
   that stays silent is only covered if your frame's grading
   discipline says what happens (the contract's own default is that
   an unverifiable claim fails).

Cover what actually gets bad PRs submitted. The failure families the
lecture named ARE the harness's scoring categories, same names as the
eval README: silent drift (the diff silently does more or less than
the posted plan, or the description claims fidelity the diff
contradicts), not tested (the evidence proves nothing observable, or
the repo's own checks were never run), unreviewable (debris or
unrelated hunks bury the change), and standards wall (the repo's
stated template and disclosure asks are ignored). Your evidence
guide's four headings map onto these one to one (plan fidelity =
silent drift, test evidence = not tested, diff quality =
unreviewable, standards and comms = standards wall), and the category
floor is scored on exactly these names plus clear accept. A rubric
that ignores a category will fail the eval packages built around
that category. And remember the honest-outcome
rule, fourth week running: a PR that honestly discloses a shortfall
can be ready; a rubric that equates "less than everything" with
"hold" fails the set.
-->

## Checks

Checks run in this order. The first three answer the three questions
every PR must answer for its reviewer: **what changed**, **how it was
tested**, and **how to verify it**. The rest cover the four failure
families (silent drift, not tested, unreviewable, standards wall).

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| what-changed | The PR description read against the diff and the issue. | A reader who sees only the description can name (a) the behavior that changes, from what to what, (b) the mechanism or place in the code that changes it, and (c) the issue it addresses. Terse is fine; all three must be present. Fails if any one is missing or can only be learned by reading the diff. | required |
| how-tested | The test-evidence section read against the plan's test plan and the issue's repro. | (a) The evidence re-runs the issue's repro (the plan's primary failing case) on the branch and shows an observable before AND after (output, exit code, rendered result) on the changed code path; AND (b) every other case the test plan names (controls, secondary cases) is reported with its outcome. A stated outcome ("controls unchanged", "same-key redefine prints the warning") is enough for these; output is not required. Fails on "tests pass", "works on my machine", or "verified locally" with no observable repro before/after; on evidence that runs only a control or an unchanged path instead of the repro; or on any test-plan case the evidence never mentions. | required |
| how-to-verify | The test-evidence section (and description) read as instructions to a reviewer. | For the repro run, a reviewer could re-run it from the PR text alone: the exact command or steps, the input (or where it comes from: the issue's file, a named fixture), and the expected-after result are all stated. Other cases need only be named. Fails if any of the three must be guessed for the repro run. | required |
| repo-checks-run | The test-evidence section read against the plan's test plan and the repo-facts block's contribution asks. | Every suite or check command the plan's test plan or the repo facts name (e.g. `cargo test`, `pytest`, `yarn test`, `go test`, fmt/clippy, pre-commit) appears in the evidence with its outcome stated. Passes if neither the plan nor the repo names one. | required |
| plan-fidelity | The diff's changed files and hunks read against the plan's scope, its "not in scope" list, and its deviation notes. | Every changed file and hunk falls inside the plan's stated scope or is covered by a deviation note, AND every step the plan promises is present in the diff or explicitly deferred by a deviation note. Fails on any unplanned file, option, flag, setting, refactor, or rename (more than the plan), or any planned step silently missing (less than the plan). An honestly noted deferral passes. A changelog, whatsnew, or similar entry the repo facts require is part of the repo's asks, not drift, even if the plan's file list omits it. | required |
| reported-changes-in-diff | Every change the PR reports, read against the diff: the title, each claim in the description (including "exactly as planned", "no other changes", "docs updated", "test added", "changelog entry added"), each commit message, and any test the evidence says was added. | Every reported change appears in the diff, in the file or place the PR says, doing what the PR says it does; every scope claim ("only touches X", "no other changes") is true of the diff; and any deviation or deferral in the plan is restated in the description. Fails on a single reported change the diff does not contain or contradicts (a promised docs update, test, or changelog entry that is absent counts), or a scope claim the diff breaks. | required |
| diff-clean | The unified diff and the commit list. | The diff contains only the change and its tests: no debug prints or logging leftovers, commented-out code or attempts, dead or unused functions, TODOs added to unrelated lines, formatting or import churn on untouched code, or unrelated hunks. Fails on any one such hunk. | required |
| template-asks-met | The repo-facts block's PR template sections, checklist items, and contribution asks, read against the title, description, and diff. | Every explicit ask is visibly met: each template section has real content specific to this PR (or an explicit "N/A: reason"); each checklist item is satisfied; a required issue-link line ("closes #N", "fixes #N") is present; a required changelog/whatsnew entry or conventional-commit title appears. Passes if the repo states no asks. Fails on any stated ask ignored or filled with boilerplate. | required |
| ai-disclosure | The repo-facts block's contribution/AI policy read against the description. Treat every package as AI-assisted work. | If the repo's stated policy requires AI disclosure, the description contains one meeting that policy's terms (e.g. tool named and extent stated). If the policy requires human-written words to maintainers, the description reads as the author's own account, not generic boilerplate. Passes if the repo states no AI policy. | required |
| commit-messages | The commit list. | Each commit message says what that commit changes (not "wip", "fix", "misc"). | preferred |

## Verdict rule

- **accept** if every `required` check grades `pass`.
- **reject** if any `required` check grades `fail` or `unclear`.
  `unclear` counts as `fail`: a claim the package does not let you
  verify is not ready to submit.
- `preferred` checks never change the verdict; report them in the
  summary only.
- Less than everything is not a fail on its own. A shortfall that the
  plan's deviation note records and the description restates passes
  `plan-fidelity` and `reported-changes-in-diff`; grade the remaining
  checks against what the PR says it delivers.
- The deciding check quoted in the output is the first failing
  `required` check in table order.
