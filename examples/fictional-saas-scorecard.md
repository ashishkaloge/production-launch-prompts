# Fictional SaaS Launch Scorecard

This is a fictional example. Acme Notes, its repository, commits, and findings
are made up. Do not copy real customer data, private URLs, credentials, or
production logs into a public scorecard.

This is the `LAUNCH_SCORECARD.md` file that
[prompt 10](../prompts/10-launch-verdict-and-scalability-audit.md) writes, after
a baseline audit and two re-audits. The [Re-audit History](#re-audit-history)
shows what each round of fixes changed.

A PRODUCTION READY verdict is an agent's judgment against these prompts. It is
not a certification, a penetration test, or a compliance sign-off.

## Project

```text
Product: Acme Notes
Repository: github.com/example/acme-notes
Branch: launch-review
Commit audited: c27a5f3
Date audited: 2026-06-10
Auditor: Founder with a codebase-aware agent
Target launch date: 2026-06-14
```

## Overall Verdict

```text
Verdict: PRODUCTION READY
Overall score: 86 / 100
Baseline score: 68 / 100
Critical blockers open: 0
High priority issues open: 1
```

## Category Scores

| Category | Prompt | Score | Verdict | Biggest Risk | File and Line |
| --- | --- | --- | --- | --- | --- |
| Architecture | 01 | 7 / 10 | READY | Some duplicated business rules | `src/notes/service.ts:88` |
| Security | 02 | 8 / 10 | READY | Rate limit is per IP, not per account | `src/middleware/rate-limit.ts:12` |
| Performance | 03 | 8 / 10 | READY | Note list renders every note on the client | `web/src/pages/notes.tsx:54` |
| Code Quality | 04 | 8 / 10 | READY | Minor debug logging remains | `src/notes/service.ts:131` |
| Database | 05 | 8 / 10 | READY | No index on `notes.updated_at` for sync | `db/schema.sql:47` |
| API Design | 06 | 7 / 10 | READY | Inconsistent error responses (H5) | `src/api/errors.ts:9` |
| Testing | 07 | 8 / 10 | READY | No end-to-end test for exports | `tests/e2e/` |
| DevOps | 08 | 8 / 10 | READY | Alerts go to one inbox | `ops/alerts.yml:3` |
| Frontend and UX | 09 | 8 / 10 | READY | Empty states need clearer next actions | `web/src/components/EmptyState.tsx:15` |
| Scalability | 10 | 8 / 10 | READY | One worker handles all exports | `src/jobs/export.ts:20` |
| Privacy and Compliance | 11 | 7 / 10 | READY | Account deletion copy is unclear | `web/src/pages/settings/delete.tsx:31` |
| Revenue or Billing | 18 | N/A | N/A | Free product at launch | |

## Critical Blockers

| ID | Issue | File and Line | Evidence | Risk | Fix | Owner | Due | Status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| B1 | Missing note ownership check | `src/api/notes/[id].ts:42` | Query filters by note ID only, not by owner or workspace | User can read another user's note | Filter by owner and workspace in the query | Founder | 2026-06-06 | CLOSED in `6b2d9e4` |
| B2 | No authorization tests | `tests/api/` | No test covers cross-user read, update, or delete | The IDOR could return unnoticed | Add cross-user read, update, and delete tests | Founder | 2026-06-06 | CLOSED in `8d41e07` |

## High Priority Fixes

| ID | Issue | File and Line | Evidence | Risk | Fix | Owner | Due | Status |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| H1 | Search has no pagination | `src/api/search.ts:17` | Returns every matching note in one response | Large workspace can slow the API | Add limit, cursor, and index | Founder | 2026-06-08 | CLOSED in `b7a0c14` |
| H2 | Rollback not documented | `docs/deploy.md` | Deploy steps have no revert path | Slow recovery during a bad deploy | Add a rollback checklist | Founder | 2026-06-08 | CLOSED in `c27a5f3` |
| H3 | Export job can duplicate files | `src/jobs/export.ts:64` | A retry re-runs the export without a job key | Duplicate user exports | Add an idempotency key | Founder | 2026-06-09 | CLOSED in `c27a5f3` |
| H4 | Workspace slugs are not unique | `db/schema.sql:22` | The `slug` column has no unique constraint | Two workspaces can share a URL | Add a unique index after a duplicate check | Founder | 2026-06-07 | CLOSED in `8d41e07` |
| H5 | Inconsistent error responses | `src/api/errors.ts:9` | Routes return three different error shapes | Clients mishandle failures | Use one error envelope | Founder | 2026-06-20 | OPEN |

## Launch Decision

```text
Decision: Launch on 2026-06-14.
Reason: Both critical blockers and four of five high priority fixes are
  closed, and the re-audit checked each fix at c27a5f3.
Risks accepted: H5 inconsistent error responses, due 2026-06-20.
Risks not accepted: Cross-user data access (B1, closed).
Rollback plan: Revert-and-redeploy checklist in docs/deploy.md.
Monitoring plan: Auth failure, API error, and export job alerts.
Next review date: 2026-06-20
```

## Re-audit History

| Run | Date | Commit Audited | Prompts Run | Score Before | Score After | Closed | Still Open | Verdict |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Baseline | 2026-06-04 | `3f9c2a1` | 01-11 | - | 68 / 100 | - | B1, B2, H1, H2, H3, H4, H5 | NOT READY |
| Re-audit 1 | 2026-06-07 | `8d41e07` | 02, 05, 07, re-audit | 68 / 100 | 79 / 100 | B1, B2, H4 | H1, H2, H3, H5 | NOT READY |
| Re-audit 2 | 2026-06-10 | `c27a5f3` | 03, 08, 10, re-audit | 79 / 100 | 86 / 100 | H1, H2, H3 | H5 | PRODUCTION READY |

At the baseline, Security and Testing scored 5 / 10, Database and DevOps
scored 6 / 10, and all four were NOT READY. Re-audit 1 stayed NOT READY because
rollback (H2) was still undocumented.
