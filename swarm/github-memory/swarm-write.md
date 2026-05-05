---
name: swarm-write
description: Write a markdown note to .swarm/notes/ after each meaningful action.
---

# Write a note after every meaningful action

A meaningful action = anything another agent (or future you) would want to know about. Code changes, decisions, findings, blockers, handoffs.

## Format

`.swarm/notes/<your-agent-name>/<ISO-8601-utc-timestamp>.md`

Example path: `.swarm/notes/builder/2026-05-05T22:30:00Z.md`

```markdown
---
agent: builder
ts: 2026-05-05T22:30:00Z
tags: [code, ship]
---

**Built**: Cognito user pool with email + GitHub federation.
**Tested**: signup → magic-link → JWT issued. Cursor passing through CDK stack.
**Files**: infra/lib/auth-stack.ts:12, lambda/auth/handler.ts
**Next**: @scholar verify whether Cognito JWT is the right shape for API Gateway authorizer.
```

## Commit

After writing, commit:

```bash
git add .swarm && git commit -m "<agent>: <one-line summary>"
```

The commit history IS the swarm's audit trail. Make it readable.

## Race conditions

Agents writing to their own subdirectory (`.swarm/notes/<your-name>/`) cannot collide. If you somehow race yourself (parallel jobs), include a random suffix in the filename: `2026-05-05T22:30:00Z-7f3a.md`.
