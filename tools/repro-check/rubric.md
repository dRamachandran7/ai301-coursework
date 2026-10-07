# Rubric: is this reproduction package ready to post?

<!--
THIS IS THE PART YOU WRITE. The skill in SKILL.md executes whatever
checks you define here. It ships empty on purpose: the judgment is your
work.

A filled rubric must contain:

1. At least one row in the checks table. Each row needs all four
   columns:
   - Check: a short name (used in the output JSON).
   - Evidence: exactly what to look at, and where in the package. Name
     the part (the claim comment, the repro report's environment
     record, the artifacts read against the issue's description, the
     repo-facts block) or a location from your
     references/evidence-guide.md. "The report" is not a source; "the
     output excerpt read against the error the issue describes" is.
   - Pass condition: a decision rule about the OUTCOME that someone
     else could apply and get your answer. Judge the thing itself (does
     the artifact show the issue's behavior?), never the write-up's
     shape (how many steps it has, how long it is, whether it uses a
     template's headings). Structure-shaped checks are what make
     graders disagree with themselves.
   - Weight: `required` (a fail here holds the package) or `preferred`
     (never changes the verdict).

2. A verdict rule below the table: how the check grades combine into
   accept (ready) or reject (hold), including how `unclear` is
   treated. The verdict space is binary. If you write no rule for
   `unclear`, the skill treats it as fail.

Cover what actually gets bad packages posted. The lecture named the
proof families: the environment is recorded, the steps are complete
and followable, the behavior shown matches the issue (not an adjacent
one), the outcome is stated honestly (an evidenced cannot-reproduce is
a pass, a confident wrong-target is not), and the words respect the
repo's conventions. A rubric that ignores a family will fail eval
packages designed around that family.
-->

## Checks

| Check | Evidence | Pass condition | Weight |
|---|---|---|---|
| lively_repo | Last 5 default-branch commit dates (repo-facts block) | Most recent commit is within 30 days | required |
| unclaimed | Assignees and linked PRs on the issue | 0 assignees and 0 open linked PRs | required |
| documented | Issue body | Body has expected vs. actual behavior,  repro step, or a clear description of the feature to add not just a title | preferred |
| scope | Tags and required files | The issue spans a reasonable scope, does not touch too many features | required  |
| policy | repo facts |If the codebase has an anti-AI policy, reject. If the issue asks to change or add something that would be against the policies of the codebase or the user's methods, reject | required|

## Verdict rule

<!-- State how the grades above combine into accept or reject, and how
unclear is treated. Example shape (write your own): "accept if every
required check passes; preferred checks never change the verdict;
unclear counts as fail." -->

If and only if all required checks pass, is an issue accepted. If something is unclear, follow the following steps:

1. Lacking Description - If the other two checks passed, this issue can be accepted, and will be up to the user's discretion
2. No recent activity on the issue, but in the codebase in general - This likely means the issue is very difficult or unclear, so reject.

If a preffered check fails, but the required ones pass, then pass the issue. For pathreview specifically, do not consider if other user's have claimed an issue.