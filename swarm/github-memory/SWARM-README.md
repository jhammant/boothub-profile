# `.swarm/` — coordination via git

This directory is the shared memory of the agent swarm working on this project.

## Layout

```text
.swarm/
├── notes/                # one subdirectory per agent
│   ├── cortex/
│   ├── builder/
│   ├── scholar/
│   └── sentinel/
├── decisions/            # canonical decisions made by Cortex
├── synthesis/            # outputs of /synthesize
└── security/             # Sentinel's running risk log
```

## Why git?

- Every write is a commit → free audit trail and rollback.
- Sandboxes are disposable; memory survives.
- No new infrastructure (every dev already has GitHub).
- Pattern attribution: Alexander Watson, *"GitHub as shared memory for parallel agents"*.

## Conventions

- Filenames: `<ISO-8601-UTC>.md` (e.g. `2026-05-05T22:30:00Z.md`).
- Note format: see `AGENTS.md`.
- Mentions: `@cortex`, `@builder`, `@scholar`, `@sentinel`.
- Commit messages: `<agent-name>: <one-line summary>`.

## Bootstrap

This file was placed by the boothub `github-memory` swarm bundle. To get a swarm running:

1. Make sure `AGENTS.md` lists your agents (it should already).
2. Each agent should have a SOUL at `.claude/agents/<name>/SOUL.md`.
3. Each agent reads with the `swarm-read` skill before acting, writes with `swarm-write` after.
4. Run `/standup` for a daily roll-up. Run `/synthesize` to compress noise into decisions.
