# Production Launch Scorecard

[Prompt 10](prompts/10-launch-verdict-and-scalability-audit.md) writes this
scorecard into the repository it audits as `LAUNCH_SCORECARD.md`. After fixes,
its re-audit prompt updates the file and adds a row to
[Re-audit History](#re-audit-history), so you can see what the fixes changed.
You can also fill it in by hand after running the prompts in
[PROMPTS.md](PROMPTS.md).

See a filled, fictional example in
[examples/fictional-saas-scorecard.md](examples/fictional-saas-scorecard.md).

If the audited repository is public, keep `LAUNCH_SCORECARD.md` out of commits
until the critical blockers are closed, because it points to open risks. Adding
it to `.git/info/exclude` keeps it local.

## Project

```text
Product:
Repository:
Branch:
Commit audited:
Date audited:
Auditor:
Target launch date:
```

## Overall Verdict

```text
Verdict: PRODUCTION READY / NOT READY
Overall score: __ / 100
Baseline score: __ / 100
Critical blockers open: __
High priority issues open: __
```

## Category Scores

| Category | Prompt | Score | Verdict | Biggest Risk | File and Line |
| --- | --- | --- | --- | --- | --- |
| Architecture | 01 | __ / 10 | READY / NOT READY | | |
| Security | 02 | __ / 10 | READY / NOT READY | | |
| Performance | 03 | __ / 10 | READY / NOT READY | | |
| Code Quality | 04 | __ / 10 | READY / NOT READY | | |
| Database | 05 | __ / 10 | READY / NOT READY | | |
| API Design | 06 | __ / 10 | READY / NOT READY | | |
| Testing | 07 | __ / 10 | READY / NOT READY | | |
| DevOps | 08 | __ / 10 | READY / NOT READY | | |
| Frontend and UX | 09 | __ / 10 | READY / NOT READY | | |
| Scalability | 10 | __ / 10 | READY / NOT READY | | |
| Privacy and Compliance | 11 | __ / 10 | READY / NOT READY / NOT RUN | | |
| Revenue or Billing | 18 | __ / 10 | READY / NOT READY / N/A | | |

Use `N/A` when a category does not apply and `NOT RUN` when its prompt has not
been run.

## Critical Blockers

| ID | Issue | File and Line | Evidence | Risk | Fix | Owner | Due | Status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| B1 | | | | | | | | OPEN |

## High Priority Fixes

| ID | Issue | File and Line | Evidence | Risk | Fix | Owner | Due | Status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| H1 | | | | | | | | OPEN |

Status is `OPEN`, or `CLOSED in <commit>` once the code at that commit shows
the fix. Keep IDs stable across re-audits so the history lines up.

## Launch Decision

```text
Decision:
Reason:
Risks accepted:
Risks not accepted:
Rollback plan:
Monitoring plan:
Next review date:
```

## Re-audit History

The first row is the baseline from prompt 10. After each round of fixes:

1. Commit the fixes.
2. Re-run the audit prompts for the categories you changed, using the Prompt
   column above. For example, run 02 after a security fix or 07 after adding
   tests.
3. In the same session, run the
   [re-audit prompt](prompts/10-launch-verdict-and-scalability-audit.md#re-audit-after-fixes).
   It checks every open item at the new commit, updates the scores, and adds
   one row here.

Keep earlier rows unchanged. A score goes up only when the code shows the fix.

| Run | Date | Commit Audited | Prompts Run | Score Before | Score After | Closed | Still Open | Verdict |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Baseline | | | | - | __ / 100 | - | | |
