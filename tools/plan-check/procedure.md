# Procedure: how this skill grades a plan package

<!--
THIS IS THE PART YOU WRITE, and it is a new kind of part. Weeks 1 and
2, SKILL.md carried a numbered workflow and you only wrote judgment
files. This week the workflow is gone from the frame: SKILL.md says
"execute procedure.md", and these are the operating steps you author.
The machinery is in your hands now.

Your operator swap is the design brief. When your executor stalled
because your rubric said WHAT to decide but not HOW to find the
evidence, that was a procedure gap. This file is where those gaps get
closed: a complete procedure lets someone who has never seen a plan
package before (a groupmate, or the skill itself) grade one exactly the
way you would.

Under each stage heading below, write the concrete steps for that
stage. The one-line note under each heading says what a complete
procedure must decide there. Write steps, not intentions: "read the
repro evidence before the plan, and note what behavior it pins down"
is a step; "understand the context" is a wish.
-->

## Read order

1. In live mode, read `scope.md` and confirm the issue is in the scoped
   repo; note any house rules. Then (both modes) read `rubric.md` and
   `references/evidence-guide.md`. List the rubric's checks and its
   verdict rule.
2. Read the whole package before grading anything, in this order:
   1. The issue context: note the reported symptom and expected
      behavior.
   2. The repro evidence: note the numbered steps, the step where
      actual diverges from expected, and the Actual line. This pins
      down the behavior every later check is measured against.
   3. The candidate plan: note its stated cause, its change and
      in/out-of-scope lines, and its test section.
   4. The candidate plan comment, then the thread highlights and the
      repo-facts block.

   Read the evidence before the plan, so the plan's claims are judged
   against what was observed, not the other way round.

## Evidence gathering

3. For each check, gather exactly the evidence the rubric names, using
   the evidence guide's map.
   - Eval mode: quote the relevant lines from the bundle. Use only the
     bundle text.
   - Live mode: take the reproduction from the student's posted repro
     comment (or the house repro pack quoted in the drafts), gather the
     issue-side evidence from the locations the guide names, and treat
     the drafts as the candidate side.
   - Record two quotes per check: one from the plan, and one from the
     repro evidence it is read against.

## Check execution

4. Run the checks in table order. Grade each one `pass`, `fail`, or
   `unclear`, with a one-line evidence quote or fact for each grade.
   - If the plan omits what a check asks for, grade it `fail`.
   - Grade `unclear` only when the package itself lacks the evidence
     needed to decide, not when you did not look.
   - Grade every check, even after a required check fails, so the
     author gets full feedback.

## Verdict assembly

5. Apply the rubric's verdict rule: `accept` only if every required
   check passes. A `fail` or `unclear` on a required check means
   `reject`. There is no third verdict.
6. For a reject, quote the first required check (in table order) that
   failed or was unclear as the deciding evidence.
7. In live mode, hold the plan comment against `voice-guide.md` and
   note any broken rule in the summary. This does not change the
   verdict.
8. Output the result in the JSON format from `SKILL.md`, as the last
   block.
