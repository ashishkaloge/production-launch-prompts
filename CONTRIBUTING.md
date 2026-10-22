# Contributing

Contributions should make this repository more useful for people reviewing any
product or app before launch.

## Good Contributions

- A prompt that catches a real launch risk.
- A clearer checklist item.
- A better scorecard field.
- A standards mapping correction.
- A safer way to use codebase-aware agents in public or private repos.
- A fictional example that teaches the workflow without exposing real data.

## What Not To Submit

- Private project files, logs, screenshots, customer data, or credentials.
- Real production incident details that are not already public.
- Prompts that tell agents to make broad rewrites before auditing.
- App-type-specific rewrites that make the repo less universal.
- Links to spam, cracked software, malware, credential theft, or unsafe mirrors.

## Pull Request Checklist

- [ ] The change keeps the repo useful for many product/app types.
- [ ] No secrets, private URLs, or customer data are included.
- [ ] Markdown checks pass.
- [ ] Relative links work.
- [ ] New examples are fictional.
- [ ] New prompts ask for file paths, severity, impact, and ranked fixes.

## Local Checks

Run:

```bash
npx --yes markdownlint-cli2 "**/*.md"
node scripts/check-links.mjs
git diff --check
```
