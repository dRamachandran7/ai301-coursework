# Evidence guide: where evidence lives in a PR package

<!--
THIS IS THE PART YOU WRITE (third week running: the map stays in your
hands). Your tool uses this guide as its map: for every kind of
evidence a rubric check names, this file says WHERE to find it in a PR
package and WHAT GOOD LOOKS LIKE when you do.

The four families below are the harness's failure categories under
the names the eval README uses: plan fidelity = silent-drift, test
evidence = not-tested, diff quality = unreviewable, standards and
comms = standards-wall. A package that fails none of them is a
clear-accept. Under each family heading below, write:

- Where it lives: the exact places to look. In an eval bundle (which
  section of the package: the plan-context block's scope pair and test
  plan, the candidate PR's diff, commits, description, or
  test-evidence section, the repo-facts block's template asks and
  stated policy). In live mode (where in your working copy and on
  GitHub: your plan.md and its deviation notes, your branch's diff,
  your draft title and description, your captured test output, the
  repo's PR template and CONTRIBUTING.md).
- What good looks like: one or two sentences someone else could apply.
  Prefer observable conditions ("every changed file falls inside the
  plan's stated boundary or a deviation note") over adjectives ("the
  diff is clean").

A rubric check whose evidence this guide cannot locate is a check
nobody else can execute, and this week that cuts three ways: your
procedure says WHEN to gather each family, this guide says WHERE, and
your SKILL.md says the tool reads both. Write the map you wish your
executor had.
-->

## Package map

Every bundle (`eval/packages/<id>.md`) has the same sections, and its
`.json` twin carries the same text as fields. Live mode has a place
for each one too. Use this table to find a part; the four families
below say what to pull from it and what good looks like. Examples are
from `calib-01` (dandavison/delta#2211, gold: accept).

| Part | Bundle section | JSON field | Live mode | calib-01 example |
|---|---|---|---|---|
| Repo asks and policy | `## Repo facts`: the `pull requests:` and `contribution policy` lines | `repo_facts.raw_markdown` | the repo's `.github/pull_request_template.md`, CONTRIBUTING.md, AI_POLICY.md | "no PR template; the contribution docs ask that PRs stay small and reference the issue"; "no stated AI policy" |
| Issue | `## Issue` (title, opener line, body) | `issue.*` | the issue page, via `gh issue view` | #2211: `themes.gitconfig` repeats `file-decoration-style` in gruvmax-fang; `configparser` errors |
| Thread | `## Thread highlights` | `thread_highlights`, `comments_total` | the issue's comments | 0 comments, so no maintainer direction to engage |
| Repro evidence | `## Plan context`, the "Repro evidence (from the accepted report)" paragraph | `plan_context_markdown` | the student's posted repro comment, or the house repro pack | `DuplicateOptionError ... already exists`; `git config --get-all` prints both values, the second wins |
| Plan scope | `## Plan context`, the "Plan:" paragraph: its steps, "Not in scope:", "Files:" | `plan_context_markdown` | `plan.md`'s change and scope sections | two steps: delete the shadowed first line, verify a strict parse; not in scope: any other theme; files: `themes.gitconfig` |
| Deviation notes | `## Plan context`, any deviation or deferral note in the plan | `plan_context_markdown` | `plan.md`'s deviation notes | none |
| Test plan | `## Plan context`, the "Test plan:" sentence | `plan_context_markdown` | `plan.md`'s test section | re-run the configparser repro, expect a clean parse; `delta --show-syntax-themes` smoke check |
| Title | `### Title` | `pr.title` | the student's draft title | "Remove duplicate file-decoration-style from gruvmax-fang theme" |
| Description | `### Description` | `pr.description` | the student's draft description | "Removes the shadowed first `file-decoration-style` line from the gruvmax-fang theme (#2211)..." |
| Commits | `### Commits` | `pr.commits` | `git log main..HEAD` | one: `b52ce1a themes: remove shadowed file-decoration-style from gruvmax-fang` |
| Diff | `### Diff`: the `--- a/` / `+++ b/` file headers and `@@` hunks | `pr.diff` | `git diff main...HEAD` | one file, `themes.gitconfig`; one hunk deleting `file-decoration-style = "#434C5E" ul` |
| Test evidence | `### Test evidence` | `pr.test_evidence` | the output the student will paste into the PR | `# before: configparser.DuplicateOptionError ...` / `# after: parsed OK`; `--get-all` now prints one value; smoke check renders unchanged |

In eval mode the bundle is the whole world: quote these sections and
nothing else. In live mode the candidate side (title, description,
test evidence) is only what the student's drafts contain.

## Plan fidelity (harness category: silent-drift)

Checks: `plan-fidelity`, `reported-changes-in-diff`, and the reading
half of `what-changed`.

- Where it lives: the plan's scope sits in `## Plan context` (the
  "Plan:" paragraph's steps, its "Not in scope:" list, its "Files:"
  list, and any deviation or deferral note). What actually changed
  sits in `### Diff` (each `+++ b/<path>` header and its `@@` hunks).
  The PR's reported changes sit in `### Title`, `### Description`,
  `### Commits` (each message), and any "test added" line in
  `### Test evidence`. Live: `plan.md` and its deviation notes
  against `git diff main...HEAD`, the draft title and description,
  and `git log main..HEAD`.
- What good looks like: every `+++ b/` path is in the plan's "Files:"
  list or a deviation note, every hunk does a step the plan names,
  and every promised step (code, test, docs, changelog) has a hunk or
  a deferral note. Every change the title, description, and commits
  report has a hunk that does it, every scope claim ("exactly as
  planned", "no other changes") holds against the diff, and every
  deferral in the plan is restated in the description.
- Silent drift looks like: a file or option outside the "Files:"
  list (more than the plan; a changelog or whatsnew entry the repo
  facts require does not count), a planned step with no hunk and no note
  (less than the plan), or a description reporting a change the diff
  lacks (a docs update, a test).
- calib-01: plan "Files: `themes.gitconfig`" against the diff's only
  header `+++ b/themes.gitconfig`; plan step one ("Remove the first,
  shadowed `file-decoration-style` line") against the hunk's single
  `-    file-decoration-style = "#434C5E" ul`; step two (verify a
  strict parse) is test work, delivered in `### Test evidence`. The
  description's reported change ("Removes the shadowed first
  `file-decoration-style` line") matches that hunk, and it makes no
  scope claim the diff breaks. No deviation notes. Passes both.

## Test evidence (harness category: not-tested)

Checks: `how-tested`, `how-to-verify`, `repo-checks-run`.

- Where it lives: the proof sits in `### Test evidence`; what it must
  prove sits in `## Plan context` (the "Test plan:" sentence, and the
  "Repro evidence" paragraph's failing output). Suite and check
  commands the repo asks for sit in `## Repo facts` (the `pull
  requests:` and `contribution policy` lines). Live: the captured
  output in the draft PR against `plan.md`'s test section and the
  repo's CONTRIBUTING.md.
- What good looks like: the issue's repro re-run on the changed path,
  showing the before (matching the repro's failure) and the after
  (matching the test plan's expectation), with the command, its input,
  and the expected-after stated so a reviewer can re-run it. Every
  other case the test plan names (controls, secondary cases) is
  reported with its outcome; a stated outcome ("controls unchanged")
  is enough there. Every suite or check command the test plan or repo
  facts name appears with its outcome. Not tested looks like: "tests
  pass" or "works on my machine" with no repro before/after, a run of
  only a control or an unchanged path, or a test-plan case the
  evidence never mentions.
- calib-01: the test plan names two cases. Case one, the configparser
  repro: the evidence gives the command (`python3 -c "import
  configparser; ... c.read('themes.gitconfig'); print('parsed OK')"`),
  the input (`themes.gitconfig`), the before
  (`configparser.DuplicateOptionError: ... already exists`, matching
  the repro), and the after (`parsed OK`). Case two, the smoke check:
  "`delta --show-syntax-themes | head` renders gruvmax-fang
  unchanged", backed by `git config --get-all` now printing exactly
  `#84786A ul`, the value the repro showed was already effective.
  Neither the test plan nor the repo facts name a suite command, so
  `repo-checks-run` passes. Passes all three.

## Diff quality (harness category: unreviewable)

Checks: `diff-clean`, `commit-messages`.

- Where it lives: `### Diff` (every `+` and `-` line in every hunk)
  and `### Commits` (each message). Live: `git diff main...HEAD` and
  `git log main..HEAD`.
- What good looks like: every changed line is part of the fix or its
  test. Debris tells, any one of which fails: added debug prints or
  logging, commented-out code or earlier attempts, dead or
  `allow(dead_code)` functions, TODOs on unrelated lines, re-indented
  or reordered lines whose content did not change, import reshuffles,
  and edits unrelated to the issue. Each commit message says what its
  commit changes, not "wip", "fix", or "misc cleanups".
- calib-01: one hunk, one deleted line, with the hunk's context lines
  (`[delta "gruvmax-fang"]`, the Powerline comment, `file-style`)
  unchanged. One commit, "themes: remove shadowed
  file-decoration-style from gruvmax-fang", naming its change. Passes
  both.

## Standards and comms (harness category: standards-wall)

Checks: `template-asks-met`, `ai-disclosure`, and the
description-alone half of `what-changed`.

- Where it lives: the asks sit in `## Repo facts`: the `pull
  requests:` line (template sections, checklist items, issue-link
  line, changelog or whatsnew entry, title convention) and the
  `contribution policy` line (including any AI-use policy and its
  terms). Explicit maintainer direction sits in `## Thread
  highlights`. Where the PR honors them: `### Title`,
  `### Description`, and `### Diff` (for a changelog file). Live:
  the repo's PR template, CONTRIBUTING.md, AI_POLICY.md, and issue
  thread against the draft title, description, and diff.
- What good looks like: each stated ask is met in place: each template
  section has content specific to this PR (or "N/A: reason"), each
  checklist item is satisfied, the issue-link line is present, and the
  changelog entry is in the diff. Where the AI policy requires
  disclosure, the description discloses on the policy's terms (tool
  and extent). With only the description, a reader can say what
  behavior changes, where in the code, and for which issue. Whether
  the description's claims match the diff is plan fidelity, above.
- calib-01: the repo asks only "that PRs stay small and reference the
  issue". The diff is one line, and the description references
  "(#2211)". "no stated AI policy", so `ai-disclosure` passes. From
  the description alone: the behavior (strict parsers "no longer
  error on the duplicate"; "rendering is unchanged"), the place (the
  "shadowed first `file-decoration-style` line from the gruvmax-fang
  theme"), and the issue (#2211). Passes all three.
