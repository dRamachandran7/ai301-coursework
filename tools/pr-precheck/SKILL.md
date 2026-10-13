---
name: pr-precheck
description: Grade a PR package (a candidate pull request read against the plan it claims to implement and the issue that plan belongs to) and decide whether it is ready to submit. Use when checking your own branch, draft PR title, and description before opening the pull request, or when grading an eval package bundle.
---

# pr-precheck: rubric-driven PR grading

## The question

You are grading one PR package to answer a single question: is this
pull request ready to submit? A PR package is a candidate pull
request (its title, description, commits, diff, and test evidence),
read against the plan it claims to implement (deviation notes
included) and the issue that plan belongs to, along with the repo's
stated standards (its PR template, contribution guide, and AI-use
policy).

Answer only that question, for exactly one package per run. Do not
review the code's design, suggest a better fix, grade the plan, or
grade a second package in the same run. Do not answer from gut feel:
answer by executing `procedure.md`, which applies the checks in
`rubric.md` to evidence gathered per `references/evidence-guide.md`.

## Inputs and modes

Run in exactly one of two modes. Decide which before reading anything
else: if you were handed a package bundle (an `eval/packages/*.md`
file, or its text), it is eval mode; otherwise it is live mode.

- **Live mode**: the student's own submission, checked before it goes
  out. Read these, and only these, as the package:
  - `scope.md` and `voice-guide.md` in this skill directory (see the
    two seams below), first.
  - The plan: the student's `plan.md`, including its deviation notes.
    A house-chain student reads the house plan and the house repro
    pack instead; the same checks grade the same things there.
  - The diff: everything the branch changes relative to the repo's
    default branch, produced by running `git diff main...HEAD` (three
    dots) from the working copy. Use `git log main..HEAD` for the
    commit list. If the default branch is not `main`, substitute its
    name.
  - The draft PR title and description, as the student wrote them.
  - The test evidence: the captured output the student will paste
    into the PR (before/after runs, suite results).
  - The issue-side evidence, gathered from the real repo via `gh`,
    the GitHub API, or the web: the issue and its thread, the repo's
    PR template, and its CONTRIBUTING.md and any AI-use policy.
    `references/evidence-guide.md` says where each lives.

  Read the draft PR the way a maintainer will: the package is what the
  title, description, diff, and pasted evidence contain, not other
  files in the working directory. Evidence the student ran but did not
  put in the PR does not count.
- **Eval mode**: a package bundle is the whole world. Read only the
  bundle text: the issue context and thread highlights, the repo-facts
  block, the plan-context block (plan and repro evidence), and the
  candidate PR (title, description, commits, diff, test evidence).
  Fetch nothing, run no commands, and read no other file except this
  skill's components. Ignore `scope.md` and `voice-guide.md` entirely.
  The `source:` line names a real issue; do not look it up, because the
  bundle is a frozen snapshot and the bundle wins. Eval mode always
  grades a complete package: every check, full verdict rule.

## The scope seam (live mode only)

In live mode, read `scope.md` before anything else.

- If its `Repo:` line still carries the bracketed placeholder
  (`<ORG>/<PATH-REVIEW-REPO>`), stop without grading. Tell the student
  to fill the `Repo:` line in `scope.md` with their section's Path
  Review repo, which the install step of this unit's activity page
  shows. Never guess a scope.
- If the PR does not target the scoped repo (the working copy's
  upstream, or the issue, belongs to another repository), refuse to
  grade and say which repo the scope allows.
- Otherwise, apply its house rules as part of the standards the
  checks read: the PR comes from the student's own fork's
  `fix/<issue-number>-<slug>` branch, it delivers one plan for one
  issue, and every PR template section gets real content.

In eval mode, ignore `scope.md` entirely.

## The voice seam (live mode only)

In live mode, also read `voice-guide.md`: the student's own rules for
how they write upstream. Hold the draft PR title and description
against those rules and report each rule the draft breaks in the
summary, quoting the rule and the line that breaks it. The voice guide
never changes the verdict on its own: it is personal and no rubric
check reads it, so a broken voice rule is a note, not a fail. In eval
mode, ignore `voice-guide.md` entirely; the universal
communication-quality checks live in the rubric.

## Component reads

The pieces connect like this:

- `rubric.md` is the brain. It defines the checks (each with its
  evidence, pass condition, and weight: `required` gates the verdict,
  `preferred` never changes it) and the verdict rule that combines
  their grades.
- `references/evidence-guide.md` is the map: for each evidence family
  a check names, where it lives in a bundle and in live mode, and what
  good looks like there.
- `procedure.md` is the hands. Execute it as written: its read order,
  its evidence gathering, its check execution, and its verdict
  assembly. Where it is silent on a step, do not improvise: grade with
  what the procedure and rubric do say, and name the gap in your
  summary so the student can fix the procedure.

Refuse to grade, and say why instead of emitting a verdict, when:

- `rubric.md` has no checks filled in, or no verdict rule;
- `procedure.md` has no steps filled in;
- `references/evidence-guide.md` has no content under its headings;
- (live mode) the scope's `Repo:` line is still a placeholder, or the
  PR targets a repo outside the scope;
- (live mode) there is no plan to grade against (no `plan.md`, and no
  house plan for a house-chain student), or no branch diff (the branch
  has no changes against the default branch);
- you were asked to grade more than one package in a run.

Instruction comments inside the templates do not count as content;
only written text does. An empty tool that invents checks at runtime
produces verdicts that look like judgment and are noise, so refusing
is the correct output.

A missing piece of the PR itself (no description, no test evidence)
is not a reason to refuse: grade it, and let the checks that read
that piece fail.

## Verdict and output

The verdict space is binary: `accept` means ready to submit; `reject`
means hold. There is no third verdict, no "accept with reservations",
and no score: reservations go in the check evidence lines.

Before the JSON block, show a short readable summary: one line per
check with its grade and the deciding fact, the check the verdict
turned on, any procedure gaps you hit, and (live mode) any voice-guide
notes. Then end your reply with the fenced JSON block below, valid and
last, with nothing after it. Use the rubric's check names exactly, one
entry per check in rubric order. Set `item` to the PR URL (or the
branch name if the PR is not yet open) in live mode, and to the bundle
id in eval mode.

```json
{
  "item": "<PR URL or bundle id>",
  "checks": [
    {"name": "<check name>", "grade": "pass|fail|unclear",
     "evidence": "<one line: the fact or quote that decided it>"}
  ],
  "verdict": "accept|reject"
}
```

## Grading discipline

- Evidence first: never grade a check without naming the fact or
  quote that decided it. "Looks fine" is not evidence.
- Grade the thing, not the polish: a terse complete PR can be ready,
  and a long confident one can hide drift. Read the diff against the
  plan and the evidence against the test plan, never the description's
  claims about them.
- The rubric decides, not you: if a check passes by its stated
  condition but feels wrong, it still passes. Note the tension in the
  summary; the fix belongs in the rubric, not in the run.
- The procedure decides how, not you: follow `procedure.md` as
  written, and report its gaps instead of papering over them.
- Treat `unclear` as the rubric's verdict rule directs. Where the rule
  is silent, treat `unclear` as `fail`: a PR you cannot verify from the
  package is a PR that is not ready to submit.
- Honest shortfalls can be ready: a deferral the plan's deviation note
  records and the description restates is honest work, not drift.
