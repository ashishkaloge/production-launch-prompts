# Production Launch Prompts for Solo Founders

21 audit-first prompts for Claude Code, Codex, and other codebase-aware agents
before real users or production traffic are involved.

This is for solo founders who need a practical launch review without enterprise
process. It works for SaaS, marketplaces, AI products, internal tools, mobile
apps, APIs, dashboards, and most software products where security, reliability,
data integrity, and user trust matter.

[![Fictional launch audit showing a not-ready verdict, a score of 68 out of 100, two critical blockers, and five high priority issues](docs/assets/fictional-launch-audit.svg)](examples/fictional-saas-scorecard.md)

This fictional preview shows the kind of clear launch decision these prompts
produce. [See the full sample scorecard](examples/fictional-saas-scorecard.md).

## Why This Exists

Most prompt collections are generic. Production launch work is not generic.

Before launch, a solo founder needs to know:

- Can one user access another user's data?
- Are secrets, tokens, or sensitive workflows exposed?
- Will the app lose data under concurrent writes?
- Are critical flows tested?
- Can the product recover from failure?
- Is the app good enough for a first real user?

These prompts turn that uncertainty into a repeatable launch checklist.

## What's Inside

| File | Use |
| --- | --- |
| [PROMPTS.md](PROMPTS.md) | 21 copy-paste prompts for codebase-aware agents |
| [prompts/](prompts) | Same prompts split into individual files |
| [CHECKLIST.md](CHECKLIST.md) | One-page launch readiness checklist |
| [SCORECARD.md](SCORECARD.md) | Template for recording audit results and fixes |
| [docs/STANDARDS_MAP.md](docs/STANDARDS_MAP.md) | How this maps to public launch/security standards |
| [examples/](examples) | Fictional sample output for contributors and users |

### Part 1: Pre-Launch Audit

Run these before your first real user.

| # | Prompt | What It Catches |
| --- | --- | --- |
| 01 | Architecture Audit | Tight coupling, dead code, unclear boundaries |
| 02 | Security Audit | IDOR, auth gaps, hardcoded secrets, injection, XSS |
| 03 | Performance Audit | N+1 queries, missing indexes, memory leaks, slow UX |
| 04 | Code Quality Audit | Unsafe types, long functions, swallowed errors |
| 05 | Database Audit | Missing transactions, weak constraints, data-loss risk |
| 06 | API Design Audit | Validation, response, and versioning risk |
| 07 | Testing Audit | Untested critical paths and missing failure cases |
| 08 | DevOps Audit | Weak CI, no rollback, poor logging, backup gaps |
| 09 | Frontend and UX Audit | Mobile, forms, accessibility, empty states |
| 10 | Launch Verdict | Final go/no-go scorecard and top fixes |

### Part 2: Product and Operations Readiness

Run prompts 11-20 after critical launch blockers are fixed. Run prompt 21 for
any AI or agent feature before the final launch verdict.

| # | Prompt | What It Improves |
| --- | --- | --- |
| 11 | Privacy and Compliance | PII, consent, retention, data flows |
| 12 | Repository and Developer Experience | README, docs, templates, setup |
| 13 | Onboarding and Activation | First-run flow, empty states, activation |
| 14 | Passwordless Auth | Magic links, OTPs, replay protection, lockouts |
| 15 | Error Handling and Resilience | Retries, timeouts, recovery |
| 16 | Analytics and User Behavior | Funnel events, activation, churn signals |
| 17 | Notifications and Communication | Email, in-app, duplicate prevention |
| 18 | Revenue and Subscription Flow | Pricing, plans, billing, entitlements |
| 19 | Documentation and Knowledge Base | User docs, FAQ, troubleshooting |
| 20 | Mobile Responsiveness | 360px through desktop readiness |
| 21 | AI and Agent Production Safety | Prompt injection, tool access, data, cost, evals |

## How to Use

Work from a clean branch in the target app:

```bash
git status --short --branch
```

Then copy one prompt from [PROMPTS.md](PROMPTS.md) or [prompts/](prompts) into
your agent. Do not run the entire file at once. The numbered names below are
short labels; the exact prompt filenames are listed in
[prompts/README.md](prompts/README.md).

With Codex:

```bash
codex "<paste one prompt from PROMPTS.md>"
```

With Claude Code, paste one prompt into the session or pass it through your
normal Claude Code CLI workflow.

Example:

```bash
codex "$(cat prompts/02-security-audit-think-like-a-hacker.md)"
```

### Existing App Quick Start

For an existing product or app, start with the highest-risk launch checks:

| Step | Exact prompt file | What to do |
| --- | --- | --- |
| 1 | [prompts/02-security-audit-think-like-a-hacker.md](prompts/02-security-audit-think-like-a-hacker.md) | Find auth, access-control, injection, XSS, secret, and privilege risks |
| 2 | [prompts/05-database-audit.md](prompts/05-database-audit.md) | Find data-loss, integrity, migration, and concurrency risks |
| 3 | [prompts/07-testing-audit.md](prompts/07-testing-audit.md) | Find missing coverage for critical user and failure paths |
| 4 | [prompts/08-devops-and-infrastructure-audit.md](prompts/08-devops-and-infrastructure-audit.md) | Find CI, deployment, rollback, logging, and backup gaps |
| 5 | [prompts/10-launch-verdict-and-scalability-audit.md](prompts/10-launch-verdict-and-scalability-audit.md) | Produce the final launch verdict and scorecard |

Use this prefix when running a prompt in an existing app:

```text
Audit only. Do not edit files yet.
Do not read env files, secrets, credentials, production logs, or customer data.
Return findings with file path, line number, severity, risk scenario, and
recommended fix.
```

After the first five audits, ask your agent to consolidate the output:

```text
Create a consolidated launch risk report from the audits already run.

Group findings into:
1. CRITICAL launch blockers
2. HIGH priority before real users
3. MEDIUM follow-up
4. LOW cleanup

For each finding include:
- source audit prompt
- file path and line number
- risk scenario
- recommended fix
- estimated effort: small / medium / large
- whether the fix is safe to do now

Do not edit files yet.
```

If the app uses AI or agents, run
[21 AI and Agent Production Safety](prompts/21-ai-and-agent-production-safety.md)
after the security audit and before the final launch verdict.

### Recommended Order

1. Run [02 Security Audit](prompts/02-security-audit-think-like-a-hacker.md)
   first.
2. Run [05 Database Audit](prompts/05-database-audit.md) second.
3. Run [07 Testing Audit](prompts/07-testing-audit.md) and
   [08 DevOps and Infrastructure Audit](prompts/08-devops-and-infrastructure-audit.md)
   before any launch decision.
4. Run prompts 01, 03, 04, 06, and 09 in any order.
5. For any AI or agent feature, run
   [21 AI and Agent Production Safety](prompts/21-ai-and-agent-production-safety.md).
6. Run
   [10 Launch Verdict and Scalability Audit](prompts/10-launch-verdict-and-scalability-audit.md)
   last.
7. Run prompts 11-20 after all critical launch blockers are resolved.

Do not go live while prompts 02, 05, 07, 08, or 10 report CRITICAL issues.
For apps with payments, also clear prompt 18. For regulated data or AI data
sharing, also clear prompt 11. For any AI or agent feature, clear prompt 21.

## Output Contract

Each prompt asks the agent to return:

- file path and line number
- severity: CRITICAL, HIGH, MEDIUM, LOW
- attack or failure scenario in plain English
- exact recommended fix
- ranked next actions
- final score for that area

Prompt 10 produces the launch scorecard:

```text
Overall verdict: PRODUCTION READY / NOT READY
Score: X/100

Category          Score   Biggest Issue
Architecture      X/10    ...
Security          X/10    ...
Performance       X/10    ...
Code Quality      X/10    ...
Database          X/10    ...
API Design        X/10    ...
Testing           X/10    ...
DevOps            X/10    ...
Frontend          X/10    ...
Scalability       X/10    ...

Critical issues blocking deployment: ...
High priority fixes for this week: ...
Top 10 fixes ranked by risk x effort: ...
```

## What Makes This Different

- Audit-first, so agents do not rewrite your app before you understand the risk.
- Built for founder-speed launches, not enterprise checklists.
- Universal product/app checks with optional coverage for high-risk flows.
- Cross-tool: works with Claude Code, Codex, Cursor, and most repo-aware agents.
- Output-focused: every prompt demands file paths, severity, impact, and fixes.

## Safety Notes

These prompts are not a replacement for professional security review, legal
advice, compliance review, or production incident planning. They are a fast way
to catch obvious and high-risk launch issues before users find them.

Never paste real secrets, private keys, customer data, production credentials,
or production logs into an AI tool. If a prompt finds a secret, rotate it.

## Author

Maintained by [Ashish Kaloge](https://github.com/ashishkaloge).

## Related Public Projects

- [Awesome Agent Instructions](https://github.com/ashishkaloge/awesome-agent-instructions)
  provides patterns and a skill for reviewing and simplifying coding-agent
  instructions while preserving project constraints.
- [Coding Agent Guidelines](https://github.com/ashishkaloge/coding-agent-guidelines)
  gives coding agents practical working rules.
- [Awesome Agentic Engineering](https://github.com/ashishkaloge/awesome-agentic-engineering)
  collects useful tools and references for building with coding agents.

## Contributing

Good contributions are practical:

- a prompt that found a real launch bug
- a sharper check for solo-founder production risk
- a better output format
- a safer way to run these prompts in real repos

Keep the repo focused. Do not submit private project files, logs, screenshots
with customer data, credentials, or production configuration.

See [CONTRIBUTING.md](CONTRIBUTING.md) before opening a pull request. For
security-sensitive reports, use [SECURITY.md](SECURITY.md).

## License

MIT. See [LICENSE](LICENSE).
