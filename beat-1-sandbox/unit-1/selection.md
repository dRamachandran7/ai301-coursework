# Unit 1 — Issue Selection

Path: `beat-1-sandbox/unit-1/selection.md`

Record of the issue carried into Unit 2, and of the evaluation runs that produced
`eval-run.txt`. This file is graded at the path above; a copy kept anywhere else in
the repository is not read.

Complete every labelled field below. Each is graded on its own; content placed under the
wrong label is not graded.

---

## Selected issue

**Issue link**

[https://github.com/codepath/pathreview-ai301-fa26-s3/issues/9](https://github.com/codepath/pathreview-ai301-fa26-s3/issues/9)

**Verdict output**

[Your skill's live-mode output for this issue, pasted verbatim and ending with the
fenced JSON verdict block. A summary does not satisfy this field.]

**The verdict must record `accept` for this issue.** Choose an issue your own skill
accepts. If your skill rejects every candidate you try, that is a signal about your
rubric rather than about the issues: revise it and re-run — retries are unlimited and a
partial re-run costs about $0.20 — or run the skill on different candidates. Output
recording `reject` for the issue you chose earns no credit for this field.

```
- lively_repo: pass. The newest commit on main is from 2026-09-16, six days ago. The limit is 30 days.
- unclaimed: pass. The issue has no assignees, no linked PRs and no comments. None of the repo's PRs mention #9 or caching.
- scope: pass. The issue names two files, rag/generator/review_generator.py and core/services/review_service.py, and both exist. It's labelled tier-2 ("Intermediate difficulty"), with an estimated effort of 4–7 hours.
- policy: pass. docs/CONTRIBUTING.md and the PR template say nothing about AI, and the repo has no AI_POLICY.md or similar file. A repo that says nothing counts as a pass. Adding a cache doesn't go against anything in the repo's guidelines.
- documented (preferred): pass. The body explains the problem (running the same review twice repeats the whole RAG pipeline). It also says what to build: a cache keyed on a hash of the portfolio's content.

The rubric still accepts it, but three things are worth knowing before you claim it:
- This is a tier-2 task of 4–7 hours, not a small starter fix. It covers two areas of the code (rag/ and core/).
- The hard part will be deciding when a stored review goes out of date. The issue only asks you to key the cache on the portfolio's content. A stored review could go stale if the model or prompt changes, so settle how to handle that before you start.
- Nobody has commented since the issue was opened 12 days ago. Your "no recent activity" rule only applies when a check comes back unclear, and none did.

{
  "item": "https://github.com/codepath/pathreview-ai301-fa26-s3/issues/9",
  "checks": [
    {"name": "lively_repo", "grade": "pass", "evidence": "Most recent default-branch commit 2026-09-16T21:50:18Z (6 days before 2026-09-22), within 30 days"},
    {"name": "unclaimed", "grade": "pass", "evidence": "assignees: []; timeline shows only 4 'labeled' events, no linked/cross-referenced PRs; no repo PR mentions #9"},
    {"name": "documented", "grade": "pass", "evidence": "Body: 'Add a cache keyed on the profile's content hash that returns the stored review if the portfolio hasn't changed.' plus relevant files listed"},
    {"name": "scope", "grade": "pass", "evidence": "Two named files (rag/generator/review_generator.py, core/services/review_service.py), label tier-2, 'Estimated effort: 4–7 hours'"},
    {"name": "policy", "grade": "pass", "evidence": "docs/CONTRIBUTING.md and PULL_REQUEST_TEMPLATE.md contain no AI restrictions; no AI_POLICY file in repo; caching request conflicts with no stated policy"}
  ],
  "verdict": "accept"
}
```

---

## Eval iterations

Quote source text directly in each field below. Paraphrase does not satisfy them.

**Run history**

14/20, 16/20, 18/20

**Issue analysis**

issue-15 |   reject | accept | NO | graded accept



My skill graded issue-15 as accept, while the gold-label says it should be a reject. I assume this is due to the scope of the problem, which the gold-label considers too wide, but my skill thinks is reasonable.

**Check rationale**

```markdown
| lively_repo | Last 5 default-branch commit dates (repo-facts block) | Most recent commit is within 30 days | required |
```

**Trade-offs**

Valid projects that have simply remain untouched for a while may be rejected, even if the project is still active in ways other than commit history (eg. active users)

---

## Selection rationale

Graded on whether all three are answered, in your own words. Not on how good the
reasoning is, and not on length — a short honest answer to each earns the full marks.
This is also the basis for the claim comment you write in Unit 2.

**Selection rationale**

[Answer all three:

1. The issue's fit to your interests and to the time available.

I'm interested in optimizing a RAG system, and creating a cache layer is a great way to do that.

1. What the verdict identified correctly, and what you weighed that the rubric could

The verdict correctly identified that the repo was live, the scope was narrow enough, and it was reasonable to complete. I also weighed that it was within my capabilities

1. The anticipated difficulty in claiming it.

This is listed as a 7-9 hour job, which may be more time than expected.

---

Related paths: `eval-run.txt` in this directory; your skill's files in
`tools/issue-select/`.