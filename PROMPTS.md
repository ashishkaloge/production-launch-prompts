# Solo Founder Production Launch Prompts

21 audit-first prompts for Claude Code, Codex, Cursor, and other codebase-aware
agents.

Use one prompt at a time. Do not run this whole file as one giant prompt.
Individual prompt files are available in [prompts/](prompts).

Global operating rules for every prompt:

- Start by running `git status --short --branch` and report the branch/status.
- Do not modify files unless the user explicitly asks for implementation.
  Prompt 10 and its re-audit are the exception: they write only
  `LAUNCH_SCORECARD.md`.
- Do not print secret values. If you find one, report only the file path, key
  name/type, and rotation risk.
- Prefer specific file paths, line numbers, and concrete fixes over broad advice.
- Separate confirmed findings from assumptions.
- End with ranked next actions.

---

## Part 1: Pre-Launch Audit Prompts

Run these before your first real user touches the product.

---

### 01. Architecture Audit

```text
You are a senior software architect doing a production readiness review.

Audit this codebase end-to-end for architecture quality.

Do not modify files yet. Produce a report first.

Check:
- Folder structure and separation of concerns
- State management patterns and whether state leaks across layers
- API layer design and whether business logic is stuck in controllers/routes
- Dead code, unused imports, and abandoned modules
- Duplicated logic that should be shared
- Circular dependencies
- Module boundaries and tight coupling
- Whether the architecture can support the next 3 product features without a rewrite

For every issue found, provide:
- File path and line number
- Severity: CRITICAL / HIGH / MEDIUM / LOW
- Current problematic code or pattern
- Recommended fix or restructure
- Why this matters before launch

End with:
- Architecture score: X/10
- Biggest structural risk before launch
- One refactor with the highest launch impact
- Top 5 actions ranked by risk x effort
```

---

### 02. Security Audit - Think Like a Hacker

```text
You are a senior penetration tester. Think like an attacker, not a developer.

Audit this codebase for security vulnerabilities before production launch.

Do not modify files yet. Produce a report first. Do not print secret values.

Check:
- Hardcoded secrets, API keys, tokens, private keys, or credentials
- Authentication gaps: routes, APIs, background jobs, and admin paths
- Authorization gaps and IDOR: can user A access user B's data by changing an ID?
- Tenant isolation: can one tenant/account access another tenant/account?
- Mass assignment: can users submit extra fields to elevate privileges?
- XSS injection vectors
- SQL/NoSQL injection
- SSRF, open redirects, unsafe file upload, and unsafe deserialization
- CSRF protection where browser cookies are used
- Rate limiting on login, OTP, password reset, API keys, admin actions, and
  other sensitive workflows
- Security headers: HSTS, CSP, X-Frame-Options, X-Content-Type-Options
- Session token entropy, storage, expiry, rotation, and logout behavior
- Whether secrets, internal paths, stack traces, or privileged fields leak in
  API responses
- Webhook signature verification for external services
- High-risk workflow abuse: account takeover, duplicate submissions, unauthorized
  actions, webhook replay, and automated abuse

For every vulnerability, provide:
- File path and line number
- Attack vector in plain English
- Severity: CRITICAL / HIGH / MEDIUM / LOW
- Exact recommended fix with before/after code where useful
- Whether this blocks launch

End with:
- Security score: X/10
- Issues that could cause immediate user data breach
- Issues that could allow account takeover or unauthorized sensitive action
- Top 10 fixes ranked by exploitability x impact
```

---

### 03. Performance Audit

```text
You are a performance engineer reviewing this codebase before production launch.

Find performance problems that will hurt real users.

Do not modify files yet. Produce a report first.

Check:
- N+1 database queries and queries inside loops
- Missing database indexes on filtered, joined, or sorted columns
- No pagination or unbounded queries on list endpoints
- Synchronous operations that should be async or queued
- Missing caching on expensive repeated operations
- Memory leaks from event listeners, timers, subscriptions, or closures
- Large unused dependencies and excessive client bundle size
- Images, fonts, and assets that are not optimized or lazy loaded
- Blocking work on the main thread
- Database connection pool configuration
- Slow cold starts or expensive app initialization
- External API calls with no timeout or backoff

For every issue, provide:
- File path and line number
- Current behavior and user impact
- Severity: CRITICAL / HIGH / MEDIUM / LOW
- Recommended fix with before/after code where useful
- How to verify the improvement

End with:
- Performance score: X/10
- One change most likely to improve response time
- Estimated user-facing impact of the top 3 issues
- Top 5 actions ranked by risk x effort
```

---

### 04. Code Quality Audit

```text
You are a senior developer doing a code quality review before this app goes live.

Find code quality problems that increase launch risk.

Do not modify files yet. Produce a report first.

Check:
- TypeScript `any`, unsafe casts, or disabled type checks
- Functions longer than 50 lines that should be split
- Nesting deeper than 3 levels
- Magic numbers and strings without named constants
- Console logs, debug flags, and development-only code left in production paths
- Naming inconsistency and unclear variables
- TODO/FIXME comments in launch-critical paths
- Commented-out code that should be removed
- Functions doing more than one thing
- Errors swallowed silently
- Business rules duplicated across frontend/backend
- Unclear ownership of shared utilities and domain logic

For every issue, provide:
- File path and line number
- Severity: CRITICAL / HIGH / MEDIUM / LOW
- Before/after fix where useful
- Why this matters before launch

End with:
- Code quality score: X/10
- Top 3 files that need the most attention
- Top 5 actions ranked by risk x effort
```

---

### 05. Database Audit

```text
You are a database engineer reviewing this schema and query layer before
production launch.

Find risks that could cause data loss, data leaks, or inconsistent state.

Do not modify files yet. Produce a report first. Do not print secret values.

Check:
- Missing indexes on foreign keys and frequently queried columns
- Missing transactions where multiple writes must be atomic
- Sensitive data stored unencrypted: passwords, tokens, API keys, secrets, PII
- Passwords or tokens stored without strong hashing
- Connection pool sizing and timeout behavior
- N+1 query patterns in ORM usage
- Hard deletes where audit history or recovery is needed
- Migrations that are irreversible, unsafe, or likely to lock large tables
- Missing created_at/updated_at/deleted_at fields on important tables
- Enum columns that should be relational tables
- Missing unique constraints and foreign key constraints
- Race conditions under concurrent users
- Multi-tenant data isolation in schema and queries

For every issue, provide:
- Table/model name, file path, and line number
- Severity: CRITICAL / HIGH / MEDIUM / LOW
- Recommended fix with SQL or ORM code where useful
- Whether this blocks launch

End with:
- Database score: X/10
- Issues that could cause data loss
- Issues that could cause data leakage
- Issues that could cause race conditions
- Top 10 fixes ranked by impact x effort
```

---

### 06. API Design Audit

```text
You are an API design expert reviewing this codebase before it goes public.

Find API issues that will break clients, leak data, or create support burden.

Do not modify files yet. Produce a report first.

Check:
- REST or RPC design consistency
- HTTP methods and status codes
- API versioning and future breaking-change risk
- Inconsistent response shapes
- Missing server-side input validation
- Error responses that are inconsistent or not machine-readable
- Missing pagination, filtering limits, and request size limits
- Internal IDs or sensitive fields exposed in public responses
- Endpoints doing too much
- Missing rate limits for expensive or sensitive endpoints
- Missing idempotency keys for mutations, orders, bookings, billing, or external
  side effects
- Webhook endpoint design and replay protection

For every issue, provide:
- Endpoint path, file path, and line number
- Severity: CRITICAL / HIGH / MEDIUM / LOW
- Recommended fix with before/after code where useful
- Client impact if left unfixed

End with:
- API design score: X/10
- Biggest breaking-change risk for future users
- Top 5 actions ranked by risk x effort
```

---

### 07. Testing Audit

```text
You are a QA engineer reviewing test coverage before this app goes live.

Find testing gaps that could let launch-critical bugs reach users.

Do not modify files yet. Produce a report first.

Check:
- Overall test coverage if measurable
- Critical paths with zero coverage: auth, data mutations, account settings, and
  high-risk workflows
- Domain-critical paths with zero coverage: orders, bookings, workflows,
  approvals, uploads, or equivalent
- Happy-path-only tests with no failure cases
- Flaky tests or tests that depend on timing/order
- Missing integration tests for external services: email, messaging, billing,
  storage, AI providers, or other integrations
- Missing E2E tests for core user flows
- Tests that assert implementation details instead of behavior
- Missing security tests: unauthorized access, tenant isolation, rate limits,
  webhook signatures
- Database transaction rollback tests
- Regression tests for the highest-risk bugs already found

For every gap, provide:
- What is untested
- Risk if this breaks in production
- Severity: CRITICAL / HIGH / MEDIUM / LOW
- Suggested test case to write
- Best test type: unit / integration / E2E / contract

End with:
- Testing score: X/10
- Three untested paths most likely to cause an incident
- Minimum test set required before launch
```

---

### 08. DevOps and Infrastructure Audit

```text
You are a DevOps engineer reviewing this project before production launch.

Find deployment, monitoring, rollback, and operations risks.

Do not modify files yet. Produce a report first. Do not print secret values.

Check:
- Environment variables and hardcoded configuration
- Secrets in git, .env files, build logs, Docker layers, or CI config
- .gitignore coverage for local secrets and generated files
- Docker images: minimal, pinned, production-optimized, non-root where possible
- CI/CD pipeline: tests, lint, typecheck, security checks before deploy
- Deployment permissions and least privilege
- Logging with enough context but no sensitive data
- Monitoring, metrics, alerting, and health checks
- Error tracking and release tagging
- Graceful shutdown and SIGTERM handling
- Database backups that are automated and tested
- Rollback procedure that can restore a previous version quickly
- Environment parity between development, staging, and production
- Single points of failure with no redundancy

For every issue, provide:
- File path or config location
- Severity: CRITICAL / HIGH / MEDIUM / LOW
- Recommended fix
- Verification step

End with:
- DevOps score: X/10
- What would happen if production failed at 3am
- Minimum launch-safe ops checklist
- Top 5 actions ranked by risk x effort
```

---

### 09. Frontend and UX Audit

```text
You are a frontend engineer and UX reviewer checking this app before real users
see it.

Find interface issues that will confuse users, break mobile, or leak sensitive data.

Do not modify files yet. Produce a report first.

Check:
- Loading states for every async action
- Error states with human-readable recovery guidance
- Empty states that tell users what to do next
- Mobile responsiveness at 360px, 390px, 768px, 1366px, and 1440px
- Accessibility: labels, alt text, keyboard navigation, focus states, contrast
- Inline form validation
- Client-side secrets in browser console, source, or network responses
- SEO/social basics for public pages: title, meta description, og:image
- Buttons disabled during loading to prevent double submission
- Long text, names, emails, and IDs breaking layout
- Destructive actions requiring confirmation
- First-run experience for a user with no data

For every issue, provide:
- Component name, file path, and line number
- Severity: CRITICAL / HIGH / MEDIUM / LOW
- Recommended fix
- User impact if left unfixed

End with:
- Frontend score: X/10
- Issue most likely to make a first-time user leave
- Top 5 actions ranked by risk x effort
```

---

### 10. Launch Verdict and Scalability Audit

The agent prints the verdict and writes it to `LAUNCH_SCORECARD.md` in the
audited repository, following [SCORECARD.md](SCORECARD.md). After fixes, run
the [re-audit](#re-audit-after-fixes) to record what changed.

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

#### Re-audit After Fixes

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

---

## Part 2: Product and Operations Readiness Prompts

Run these after launch blockers are understood.

---

### 11. Privacy, Compliance, and Data Retention Audit

```text
You are a privacy-minded product engineer reviewing this app before launch.

Find engineering gaps that could create privacy, compliance, or user-trust risk.

This is not legal advice. Focus on product, data, and implementation risks.

Do not modify files yet. Produce a report first. Do not print sensitive values.

Check:
- What personal data is collected and where it is stored
- Whether each data field has a clear product purpose
- PII in logs, analytics, support tools, session replay, or error tracking
- Consent for email, marketing, cookies, analytics, and notifications
- Data retention and deletion behavior
- User export and account deletion flows
- Access control around admin/support views
- Audit logs for sensitive actions
- Privacy policy and terms links in signup, checkout, and footer
- Cookie banner or consent mode where required
- Billing data handling and whether card data is kept out of the app when
  payments exist
- Regulated or high-risk product assumptions: consent, age gates, disclaimers,
  region restrictions, professional claims, or safety warnings
- AI-specific risks: user data sent to model providers, prompt logging, opt-out
  controls

For every issue, provide:
- File path, route, model, or product surface
- Severity: CRITICAL / HIGH / MEDIUM / LOW
- Risk in plain English
- Recommended product or code fix
- Whether legal/compliance review is needed

End with:
- Privacy/compliance readiness score: X/10
- Data flows that need immediate review
- Minimum privacy-safe launch checklist
```

---

### 12. Repository and Developer Experience

```text
You are a developer experience engineer.

Make this repository clean, professional, and contributor-ready.

Do not modify files yet. Produce a report first.

Review and recommend improvements for:
- README.md: clear project description, stack, setup in under 5 minutes, screenshots
- CONTRIBUTING.md: issues, branch naming, PR process
- .github/ISSUE_TEMPLATE: bug report and feature request templates
- .github/PULL_REQUEST_TEMPLATE.md
- CHANGELOG.md
- .env.example with descriptions and no real values
- Makefile or package scripts for common commands
- Architecture documentation or ADRs
- API documentation: OpenAPI, Postman, or equivalent
- Code of conduct and license
- CI badge and status visibility
- Dependency freshness and known CVEs
- .gitignore coverage for secrets and generated files

For every recommendation, provide:
- File to create or update
- Priority: HIGH / MEDIUM / LOW
- Why it matters
- Proposed content or exact diff if small

End with:
- Repository readiness score: X/10
- Minimum public/professional launch checklist
```

---

### 13. User Onboarding and Activation Flow

```text
You are a product growth engineer focused on user activation.

Audit and redesign the onboarding flow so a new user reaches value quickly.

Do not modify files yet. Produce a report first.

First define activation for this app: the moment a user has done the one thing
that proves the product has value for them.

Review:
- Current first screen after signup
- Number of steps before activation
- Screens with no clear next action
- Drop-off points if analytics exist
- Progress indicator during setup
- Empty states and whether they guide the first action
- Guided setup for the product's core workflow
- Tooltips or coach marks for complex UI
- Setup checklist for the product's required first actions
- Email onboarding sequence if one exists

Provide:
- Current onboarding diagnosis
- Redesigned onboarding flow step by step
- Copy for each screen
- Empty state copy for every blank page
- Day 0, Day 1, and Day 3 onboarding emails
- Code changes needed to implement
- Activation metric to track

End with:
- Onboarding readiness score: X/10
- Fastest improvement likely to increase activation
```

---

### 14. Passwordless Auth - Magic Link and OTP

```text
You are a security and UX engineer improving the authentication flow.

Audit the current passwordless authentication implementation.

Do not modify files yet. Produce a report first. Do not print token values.

Check:
- OTP expiry, maximum 10 minutes unless there is a strong reason
- OTP length and entropy
- Brute force protection and lockout
- Magic link expiry and single-use behavior
- Replay attack prevention
- Whether OTP or magic links can be reused
- Timing attack protection on token comparison
- OTP and magic link tokens stored hashed, not plaintext
- Whether requesting a new OTP invalidates the previous one
- Session creation after verification
- Device/session revocation
- User-facing copy for "if you did not request this, ignore it"
- Rate limiting per account, IP, and destination

For every issue, provide:
- File path and line number
- Severity: CRITICAL / HIGH / MEDIUM / LOW
- Recommended fix with before/after code where useful
- Whether this blocks launch

Also provide:
- Ideal OTP email template
- Ideal magic link email template
- Minimal tests required before launch

End with:
- Passwordless auth readiness score: X/10
- Top 5 actions ranked by security impact
```

---

### 15. Error Handling and Resilience

```text
You are a reliability engineer reviewing error handling in this codebase.

Find places where errors are swallowed, hidden, or handled poorly.

Do not modify files yet. Produce a report first.

Check:
- Empty catch blocks
- Generic user messages with no recovery path
- Missing frontend error boundaries
- External API calls with no timeout
- Missing retry logic for transient failures
- Unhandled promise rejections
- Missing fallback UI for failed data loads
- Error logging with too little context or too much sensitive data
- User error vs system error vs external service error
- WebSocket disconnect and reconnect behavior
- Queue failures and retry/dead-letter handling
- Billing, order, booking, approval, upload, or domain-critical failures

For every issue, provide:
- File path and line number
- What currently happens when this fails
- Severity: CRITICAL / HIGH / MEDIUM / LOW
- Recommended fix with before/after code where useful
- How to verify the fix

End with:
- Resilience score: X/10
- Failures most likely to cause support tickets
- Failures most likely to cause data or revenue loss
```

---

### 16. Analytics and User Behavior Tracking

```text
You are a growth analyst setting up product analytics for a new app.

Design meaningful analytics that tell the founder where users succeed and fail.

Do not modify files yet. Produce a report first. Do not include PII or secrets.

Set up or recommend:
- Page view tracking on key routes
- Core funnel events:
  - user_signed_up
  - onboarding_started
  - onboarding_completed
  - first_core_action_completed
  - trial_started or plan_selected
  - paid_plan_started or billing_succeeded
  - billing_failed
  - user_churn_risk_detected
- Domain events for this product's most important workflow
- Error events for failed critical actions
- Activation, retention, and revenue metrics
- Privacy-safe session recording only if appropriate
- Daily active users and weekly active users

For each event, provide:
- Event name in snake_case
- Properties to capture
- Properties to avoid
- Where in the codebase to instrument it
- Implementation approach

Also provide:
- Funnel definition
- Dashboard outline for a solo founder
- Best free or low-cost analytics tool for this app

End with:
- Analytics readiness score: X/10
- The 10 events to implement first
```

---

### 17. Notification and Communication System

```text
You are a communication systems engineer reviewing how this app talks to users.

Audit all user-facing notifications and communications.

Do not modify files yet. Produce a report first.

Check:
- Transactional emails for signup, login, verification, billing, security, and
  major product events
- Email templates that are mobile-responsive, branded, and clear
- In-app notifications that are grouped, dismissible, and actionable
- Messaging integrations such as Slack, Discord, SMS, chat, or messaging apps if
  present
- Notification preferences
- Duplicate notification prevention
- User timezone handling
- Unsubscribe behavior for non-transactional messages
- Security notifications for sensitive account changes
- Failed billing, renewal, cancellation, and refund messages when billing exists
- Whether notification content leaks sensitive data

For each missing or broken notification, provide:
- Trigger event
- Channel
- Template copy
- Implementation location
- Priority: HIGH / MEDIUM / LOW

End with:
- Notification readiness score: X/10
- Missing messages most likely to hurt trust or revenue
- Minimal launch-safe notification set
```

---

### 18. Revenue, Billing, and Subscription Flow

```text
You are a revenue engineer reviewing billing, pricing, and subscription flows.

Audit the complete revenue flow for correctness, security, and conversion.

Do not modify files yet. Produce a report first. Do not print secrets.

Check:
- Pricing page clarity
- Trial, freemium, or activation-before-billing flow
- Billing failure handling
- Subscription or plan renewal reminders where applicable
- Cancellation and downgrade behavior
- Plan limits enforced server-side
- Billing or external-service webhook signature verification
- Billing or external-service webhook idempotency
- Duplicate event handling
- Refund flow
- Receipt delivery
- Failed billing retry policy
- Tax/VAT handling if relevant
- Whether card data is handled only by the payment provider
- Access changes after purchase, renewal failure, cancellation, or refund
- Non-payment revenue models: lead capture, usage quotas, credits, seats,
  upgrades, sponsor links, or paid support

For every issue, provide:
- File path and line number
- Severity: CRITICAL / HIGH / MEDIUM / LOW
- Recommended fix
- Whether this blocks launch

Also provide:
- Pricing page copy improvements
- Billing failure email template
- Renewal reminder email template
- Minimal tests required before launch

End with:
- Revenue readiness score: X/10
- Revenue or security issues that block launch
```

---

### 19. Documentation and Knowledge Base

```text
You are a technical writer creating documentation for this product.

Create documentation that lets a new user succeed without contacting support.

Do not modify files yet. Produce a report first.

Create or recommend:
- Getting started guide from signup to first meaningful action
- Product walkthrough for the core workflow
- Account and billing guide
- Security best practices for users
- API key or external integration guide if relevant
- Notification setup guide if relevant
- FAQ: top 10 questions a new user will ask
- Troubleshooting guide: top 10 errors and fixes
- Glossary for domain-specific terms
- In-app tooltip copy for complex UI elements

Format each guide as:
- Markdown compatible with a docs platform
- Clear headings
- Step numbers for sequential actions
- Warning boxes for anything involving money, security, or irreversible actions
- Tip boxes for best practices

End with:
- Documentation readiness score: X/10
- Docs needed before launch
- Docs that can wait until after launch
```

---

### 20. Mobile Responsiveness - Full Audit and Fix Plan

```text
You are a mobile UX engineer doing a complete responsive design audit.

Test every important route at these viewport widths: 360px, 390px, 768px,
1366px, 1440px.

Do not modify files yet. Produce a report first.

For each route, check:
- No horizontal overflow or page-level horizontal scroll
- Navigation is reachable and tappable
- Buttons have minimum 44px touch targets
- Text is readable, minimum 16px on mobile
- Tables scroll inside their container, not the whole page
- Charts resize without clipping axes or tooltips
- Modals fit within viewport and scroll internally
- Forms fit without horizontal scroll
- Long text truncates cleanly
- Sticky elements do not cover important content
- Bottom navigation does not cover action buttons
- Mobile keyboard does not push key content off screen
- Loading states remain visible on slow mobile connections

For every breakpoint failure, provide:
- Route affected
- Viewport width where it breaks
- File path and line number
- CSS or component fix
- Before/after code where useful
- Severity: CRITICAL / HIGH / MEDIUM / LOW

End with:
- Mobile readiness score: X/10 per viewport
- Top 5 mobile fixes ranked by user impact
- Whether mobile issues block launch
```

---

### 21. AI and Agent Production Safety

```text
You are reviewing an AI feature or agent before production launch.

Find ways it could leak data, take an unsafe action, waste money, or fail
without a safe recovery.

Do not modify files yet. Produce a report first. Do not print secret values,
customer data, or sensitive model input and output content.

If this product has no AI or agent feature, say not applicable and stop.

Check:
- Direct and indirect prompt injection from users, web pages, documents,
  retrieval, memory, and tool results
- Whether untrusted content is kept separate from system instructions and
  cannot change permissions
- Provider retention, training, region, and deletion settings, including any
  gap between code, configuration, and provider terms
- The exact context sent to each provider and whether secrets, credentials,
  private files, other users' data, and unrelated history are excluded
- Tool allowlists, input validation, least privilege, tenant isolation, and
  authorization checks on the server
- Human approval before payments, deletion, account changes, messages,
  deployments, or other irreversible or external actions
- Request, token, tool step, time, and concurrency limits, plus per user or
  workspace budgets, alerts, and a kill switch
- Evals for normal use, prompt injection, authorization bypass, bad tool
  output, provider failure, refusal, and regressions
- Safe fallback behavior when a model, provider, or tool fails or returns an
  invalid or uncertain result
- Output validation before model output is used as code, SQL, HTML, a command,
  or tool input
- Logs and traces that support debugging without storing sensitive context

For every issue, provide:
- File path and line number, provider setting, or runtime control
- Confirmed evidence or an explicit unknown
- Severity: CRITICAL / HIGH / MEDIUM / LOW
- Abuse or failure scenario in plain English
- Exact recommended fix
- Whether this blocks launch

End with:
- AI and agent safety score: X/10
- Critical launch blockers
- Actions that require human approval
- Data sent to each provider and its retention status
- Minimum eval and fallback set required before launch
- Top 5 fixes ranked by risk x effort
```

---

## Recommended Run Order

1. 02 Security Audit
2. 21 AI and Agent Production Safety, when applicable
3. 05 Database Audit
4. 07 Testing Audit
5. 08 DevOps and Infrastructure Audit
6. 01 Architecture Audit
7. 03 Performance Audit
8. 04 Code Quality Audit
9. 06 API Design Audit
10. 09 Frontend and UX Audit
11. 10 Launch Verdict and Scalability Audit, which writes `LAUNCH_SCORECARD.md`
12. Fix the blockers, then run the re-audit after fixes from prompt 10
13. 11-20 after critical launch blockers are fixed

Do not go live while prompts 02, 05, 07, 08, or 10 report CRITICAL issues.
For apps with payments, also clear prompt 18. For regulated data or AI data
sharing, also clear prompt 11. For any AI or agent feature, clear prompt 21.
