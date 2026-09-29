Evidence guide: where proof lives in a reproduction package

This is the rubric's map. For every kind of proof a check names, it says
WHERE to look (in an eval bundle, and live on GitHub) and WHAT GOOD
LOOKS LIKE when you find it. An eval bundle has these parts, in order:
## Repo facts, ## Issue (title, labels, author, dates, body),
## Thread highlights, ## Candidate claim comment, and
## Candidate repro report. In eval mode those parts are the whole
world; never fetch anything.

Rubric check map

One entry per check in rubric.md: where its evidence lives and the
observable condition that decides it.

lively_repo





Where it lives: eval, the ## Repo facts block (last default-branch
commit dates, or failing that the latest release date) compared to
the bundle's capture date in that block's heading. Live, gh api repos/<owner>/<repo>/commits?per_page=5 on the default branch,
compared to today's date.



What good looks like: the most recent commit (or release, if commits
are not listed) is dated within 30 days of the capture/today date.
An archived repo (archived: yes) fails regardless of dates. If no
date appears anywhere in the block, grade unclear.

unclaimed





Where it lives: eval, ## Thread highlights (comments saying "I'll
take this", "pushed a fix", "opened a PR") and any assignee/linked-PR
line in ## Issue. Live, the issue sidebar (Assignees, Development)
and gh issue view <n> --json assignees plus linked PRs in the
timeline.



What good looks like: zero assignees and zero open linked PRs. A
comment claiming a fix is already pushed counts as a linked PR even
without a link. Path Review house rule (live mode): classmates' claim
comments and PRs on the Path Review repo do not count as claims;
ignore them and grade only non-classmate assignees and PRs.

documented





Where it lives: eval, the body under ## Issue. Live, the first post
of the issue thread.



What good looks like: the body contains at least one of: expected vs.
actual behavior, a reproduction step, or (for a feature) a concrete
description of what to add. A title-only issue or a one-line "X is
broken" with no detail fails.

scope





Where it lives: eval, the issue's labels and body (which commands,
files, flags, or UI panels it names) plus the claim comment's stated
plan. Live, the same, plus a quick look at which files the named code
path lives in.



What good looks like: the issue names one feature or code path and a
fix plausibly touches a handful of files in one area. Fail if it
spans several independent features, is labeled as an epic, tracking,
refactor, or redesign issue, or the body asks for an architectural
change.

policy





Where it lives: eval, the contribution policy and bug reports
lines of ## Repo facts. Live, the repo's CONTRIBUTING.md, AI-use
policy, CODE_OF_CONDUCT.md, and issue/PR templates.



What good looks like: the repo has no anti-AI contribution policy,
and what the issue asks for does not contradict a stated repo policy
(e.g. a feature the maintainers say they will not accept, or a change
to something marked out of scope). If the policy requires AI-use
disclosure rather than forbidding AI, this check still passes, but see
Comms below: the comment must disclose.

Environment





Where it lives: the first lines of ## Candidate repro report
(tool version, install method, OS, runtime/dependency versions),
compared against the version and platform stated in the issue body
and the repo-facts block's latest release and bug reports
template line. Live, the student's draft repro comment.



What good looks like: the report names at least the tool version and
OS, plus any version the repo's bug template asks for. If that
version or platform differs from the issue's, the report says so out
loud ("filed on v0.63.1 on Termux; reproduced on 0.64.1 on Ubuntu").
A silent deviation, or no environment record at all, fails.

Steps





Where it lives: the command block or numbered steps in
## Candidate repro report, read against the steps or trigger in the
issue body.



What good looks like: a stranger could start from nothing (fresh
directory, git init, a sample file, a named input) and reach the
trigger by running exactly what is written. The steps include the
issue's actual trigger (the same flag, syntax, key, or input shape);
steps that swap in a nearby trigger, or that say "then do the thing"
without the command, fail.

Behavior shown





Where it lives: output excerpts, logs, exit codes, and screenshots in
## Candidate repro report, read against the symptom in the issue
body (error text, exit code, wrong output, missing effect).



What good looks like: the artifact shows the same symptom the issue
describes (same error or panic class, same exit code, same missing
header, same no-op), ideally beside a control run that behaves
correctly. An artifact of a different failure (a graceful validation
error where the issue reports a crash; exit 1 where the issue reports
exit 101) is an adjacent behavior and fails, however the report
narrates it. A report with no artifacts at all ("I ran it and it
failed") fails.

Honesty





Where it lives: the claims in ## Candidate claim comment and the
conclusion lines of ## Candidate repro report, set against the
artifacts in the same report.



What good looks like: every claim is backed by an artifact on the
page. "Reproduced" matches output that shows the symptom; "could not
reproduce on X" with the output shown is honest and passes. Fail when
the words claim more than the artifacts show: a reproduction claimed
with no matching output, a root cause asserted without evidence, or
a different failure narrated as the reported one.

Comms





Where it lives: ## Candidate claim comment read against the issue
(does it talk about this issue?), the ## Repo facts contribution
policy line (AI-use disclosure, selective review of outside PRs), the
bug reports template line, and ## Thread highlights (is someone
already on it?). Live, the repo's CONTRIBUTING.md and templates, and
the student's draft.



What good looks like: the comment names this issue's specific symptom
and a modest, concrete plan; it does not over-promise ("I'll fix this
today and refactor the module"). If the repo's policy requires AI-use
disclosure, the comment contains that disclosure; a missing required
disclosure fails regardless of quality. Boilerplate that could be
pasted on any issue ("Hi, I'd love to work on this!") fails.