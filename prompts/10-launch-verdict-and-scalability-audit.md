# 10. Launch Verdict and Scalability Audit

Copy this prompt into Claude Code, Codex, Cursor, or another codebase-aware agent.
Run it from the root of the product or app you want to review.

The agent prints the verdict and writes it to `LAUNCH_SCORECARD.md` in the
audited repository, following [SCORECARD.md](../SCORECARD.md). After fixes, run
the [re-audit](#re-audit-after-fixes) to record what changed.

## Prompt

```text
You are a systems engineer giving the final launch go/no-go verdict.

Assume this app gets 10x current traffic tomorrow. What breaks first?

Do not modify application code, configuration, tests, or dependencies. Produce
a report first. The only file you may write is the scorecard described at the
end.

Check:
- Single points of failure
- Session storage if more than one server runs
- File uploads and whether they depend on local disk
- Background jobs and duplicate work with multiple workers
- Missing idempotency keys on critical operations: billing, orders, bookings,
  approvals, uploads, or mutations
- Missing circuit breakers for external APIs: billing, email, messaging,
  storage, AI providers, or other integrations
- Database connection pool exhaustion
- Missing rate limiting per user/account, not only per IP
- Cron jobs that overlap
- WebSocket connection limits and reconnection behavior
- Queue backpressure and retry behavior
- Cache invalidation and stale data risk
- Feature flags or kill switches for risky flows

If previous audit reports exist, synthesize them. If not, audit fresh.

For every issue, provide:
- File path and line number
- What breaks and at what scale
- Severity: CRITICAL / HIGH / MEDIUM / LOW
- Recommended fix
- Whether this blocks launch

End with this exact format:
- Overall verdict: PRODUCTION READY / NOT READY
- Overall score: X/100
- Architecture score: X/10
- Security score: X/10
- Performance score: X/10
- Code quality score: X/10
- Database score: X/10
- API design score: X/10
- Testing score: X/10
- DevOps score: X/10
- Frontend score: X/10
- Scalability score: X/10
- Critical issues blocking deployment
- High priority fixes for this week
- Top 10 fixes ranked by risk x effort

Then write the scorecard to LAUNCH_SCORECARD.md at the repository root, or to
the path I name. Use these sections in this order:
- Project: product, repository, branch, commit audited (git rev-parse --short
  HEAD, plus "with uncommitted changes" if the working tree is not clean),
  date audited (YYYY-MM-DD), auditor (agent and model), target launch date
- Overall Verdict: verdict, overall score, baseline score, critical blockers
  open, high priority issues open
- Category Scores: a table of category, prompt, score, verdict, biggest risk,
  and file and line. Use the ten categories above as prompts 01 to 10 in that
  order, plus Privacy and Compliance (prompt 11) and Revenue or Billing
  (prompt 18). Write N/A when a category does not apply and NOT RUN when its
  audit has not been run.
- Critical Blockers and High Priority Fixes: tables of ID (B1, B2... and H1,
  H2...), issue, file and line, evidence, risk, fix, owner, due, and status
- Launch Decision: decision, reason, risks accepted, risks not accepted,
  rollback plan, monitoring plan, next review date
- Re-audit History: a table of run, date, commit audited, prompts run, score
  before, score after, closed, still open, and verdict, starting with a
  Baseline row

Scorecard rules:
- Evidence is one line on what the code does at that file and line. Never
  include secret values, credentials, customer data, or production log lines.
- Status is OPEN, or CLOSED in <commit> once the code shows the fix.
- Leave owner, due, and launch decision fields that need my judgment blank.
- If LAUNCH_SCORECARD.md already exists, keep its baseline score, item IDs,
  and Re-audit History rows. Update the other sections and add a new
  Re-audit History row instead of a second Baseline row.
- Do not commit the file. Tell me its path. If the repository is public, warn
  me that the file points to open risks.
```

## Re-audit After Fixes

After you fix items from `LAUNCH_SCORECARD.md`, commit the fixes and re-run the
audit prompts for the categories you changed (the Prompt column in the
scorecard). Then paste this prompt into the same session.

```text
Re-audit this repository after fixes. Do not modify application code. The
only file you may write is LAUNCH_SCORECARD.md.

Read LAUNCH_SCORECARD.md. If it does not exist, stop and tell me to run the
launch verdict prompt first.

1. Run git status --short --branch and git rev-parse --short HEAD. This is
   the commit audited.
2. For every OPEN item in Critical Blockers and High Priority Fixes, read the
   cited file and line at this commit. Mark it CLOSED in <commit> only when
   the code shows the fix; find that commit with git log since the last
   commit audited. Otherwise keep it OPEN and update its evidence.
3. Use the audit reports I re-ran in this session. If there are none,
   re-check the categories with open items or files changed since the last
   commit audited. Add new issues with new IDs.
4. Re-score the categories you checked and the overall score. Do not raise a
   score without evidence.
5. Update the Project commit and date, Overall Verdict, Category Scores, and
   item statuses. Add one Re-audit History row: date, commit audited, prompts
   run, score before, score after, closed, still open (mark new items with
   "new"), and verdict. Do not change earlier rows.
6. Report the score before and after, the items closed, and the items still
   open. Do not commit the file.
```
