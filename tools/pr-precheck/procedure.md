# Procedure: how this tool grades a PR package

<!--
THIS IS THE PART YOU WRITE (second week running for the procedure).
Week 3 you wrote these steps for a plan package; this week the graded
object is a PR package, and the read that matters most is a
side-by-side: the diff against the plan, the evidence against the test
plan, the description against both. Your week-3 procedure is the
pattern; do not paste it unchanged, because its read order was built
for a different object.

Your rotation is the design brief again, and this week friction routes
three ways: a stall on WHAT to decide is a rubric gap, a stall on
WHERE to look is a procedure gap (this file), and a stall on what the
tool even reads or outputs is a frame gap (your SKILL.md). A complete
procedure lets someone who has never seen a PR package before grade
one exactly the way you would.

Under each stage heading below, write the concrete steps for that
stage. The one-line note under each heading says what a complete
procedure must decide there. Write steps, not intentions: "read the
plan's scope pair before opening the diff, and list the files the plan
names" is a step; "understand the change" is a wish.
-->

## Read order

1. In live mode, read `scope.md` and confirm the PR targets the scoped
   repo; note the house rules. Then read `voice-guide.md` and set it
   aside until step 12. In eval mode, skip both files.
2. In both modes, read `rubric.md` and `references/evidence-guide.md`.
   List the rubric's checks in table order and its verdict rule.
3. Read the whole package before grading anything, in this order:
   1. The issue context and thread highlights: note the reported
      symptom, the expected behavior, and any explicit maintainer
      direction.
   2. The plan context: note the repro steps and the observed
      Actual; the plan's change, its scope and "not in scope" lines,
      the files it names, every step it promises (code, tests, docs,
      changelog), its deviation or deferral notes, and every case and
      command in its test plan.
   3. The diff and the commit list: list every changed file, and for
      each file the hunks and what each hunk does. Do this before
      reading the description.
   4. The PR title and description: note every change it reports
      and every scope claim ("exactly as planned", "no other
      changes"), word for word.
   5. The test evidence: note each run, its command, its input, and
      whether it shows a before, an after, and an expected-after.
   6. The repo-facts block (live: the PR template, CONTRIBUTING.md,
      and AI policy): list every explicit ask, one per line.

   Read the plan before the diff, and the diff before the
   description. The diff is judged against the plan, and the
   description is judged against the diff; reading the description
   first lets its claims tell you what the diff "should" contain, and
   a confident description over a drifting diff then passes.

## Evidence gathering

4. Gather the evidence for every check as a set of pairings, using
   the evidence guide's map for where each part lives.
   - Eval mode: quote lines from the bundle only. Fetch nothing.
   - Live mode: take the diff from `git diff main...HEAD` and the
     commits from `git log main..HEAD`; take the issue thread,
     template, and policy from the real repo; take the title,
     description, and test evidence from the student's drafts only.
5. Build these pairings and write them down before grading:
   - **Diff against plan** (`plan-fidelity`): a two-column list. For
     each changed file and hunk, the plan line or deviation note that
     covers it, or "none". For each step the plan promises, the hunk
     that delivers it, the deviation note that defers it, or "none".
   - **Reported changes against diff**
     (`reported-changes-in-diff`): for every change the title,
     description, commit messages, or evidence reports, the hunk that
     contains it, or "absent". For every scope claim, any hunk that
     breaks it. For every deferral in the plan, the description line
     that restates it, or "not restated".
   - **Description alone** (`what-changed`): from the description
     only, without the diff, write the behavior that changes (from
     what to what), where in the code, and the issue it addresses.
     Leave a blank for anything the description does not say.
   - **Evidence against test plan** (`how-tested`): first, the
     issue's repro: the evidence run that re-runs it, with its before
     and after quoted, or "not shown", and whether it exercises the
     changed code path or only a control. Then, for each other case
     the test plan names (controls, secondary cases), the evidence
     line that reports its outcome (a stated outcome counts), or "not
     mentioned".
   - **Evidence as instructions** (`how-to-verify`): for the repro
     run, the command or steps, the input, and the expected-after, or
     "must guess" for any missing one. Other cases need only be named.
   - **Named commands against evidence** (`repo-checks-run`): each
     suite or check command the test plan or repo facts name, with
     the outcome the evidence states for it, or "not run".
   - **Debris scan** (`diff-clean`): every hunk that is a debug
     print, commented-out code, a dead function, an unrelated TODO,
     formatting or import churn, or an unrelated change, quoted.
   - **Asks against PR** (`template-asks-met`, `ai-disclosure`): for
     each ask from step 3.6, where the title, description, or diff
     meets it, or "ignored". For an AI policy, quote the policy's
     terms and the description's disclosure, or "none". Treat every
     package as AI-assisted work.
   - **Commit messages** (`commit-messages`): each message, and
     whether it says what its commit changes.

## Check execution

6. Run the checks in rubric table order. Grade each `pass`, `fail`,
   or `unclear` by applying its pass condition to its pairing from
   step 5, and record one line of evidence: the quote or fact that
   decided it. A single "none", "absent", "not shown", "not mentioned",
   "must guess", "not run", or "ignored" entry in a check's pairing fails that
   check, unless the pass condition says otherwise (a check with no
   stated ask or named command passes).
7. If the package does not contain what a check asks for (no test
   evidence, no description), grade it `fail`: the absence is the
   evidence. Grade `unclear` only when the package contains the
   evidence but it cannot decide the condition (a run whose output is
   truncated, a hunk whose effect the diff does not show). Never
   grade `unclear` because you did not look.
8. Grade each check from its own pairing; do not re-read the whole
   package per check. Go back to the package only to settle a
   specific entry in a pairing.
9. Grade every check, even after a required check fails, so the
   author gets full feedback.

## Verdict assembly

10. Apply the rubric's verdict rule: `accept` only if every
    `required` check grades `pass`. Any `fail` or `unclear` on a
    required check means `reject`. `preferred` checks never change
    the verdict. There is no third verdict.
11. For a reject, the deciding check is the first `required` check,
    in rubric table order, graded `fail` or `unclear`. Name it in the
    summary and quote its evidence line. For an accept, say that every
    required check passed.
12. In live mode, hold the PR title and description against
    `voice-guide.md` and note each broken rule in the summary, quoting
    the rule and the offending line. This never changes the verdict.
13. Write the summary (one line per check, the deciding check, any
    procedure gaps you hit, and live-mode voice notes), then output
    the JSON block from `SKILL.md`, with one entry per check in rubric
    order, as the last thing in the reply.
