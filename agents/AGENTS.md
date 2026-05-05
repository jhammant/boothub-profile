# Agents in this project

This project uses a multi-agent setup coordinated by markdown notes in `.swarm/notes/`.

## Roster

| Agent | Channel | Cadence | Owns |
|---|---|---|---|
| **Cortex** | `#cortex` | every 30 min | strategy, prioritisation, decision logs |
| **Builder** | `#builder` | on demand | implementation, refactors, code review |
| **Scholar** | `#scholar` | hourly | research, library evaluation, citations |
| **Sentinel** | `#sentinel` | continuous | security, dependency audits, secrets hygiene |

Each agent's persona is in `.claude/agents/<name>/SOUL.md`.

## Coordination protocol

1. Before starting any task, read the latest notes under `.swarm/notes/<your-name>/` and the most recent 5 across all agents.
2. After each meaningful action, write a note: `.swarm/notes/<your-name>/<iso-timestamp>.md`.
3. Commit notes with `git add .swarm && git commit -m "<your-name>: <one-line summary>"`.
4. To request another agent's input, mention them in your note: `@scholar please verify…`.
5. Synthesis: `/synthesize` aggregates the last N notes into a single decision document at `.swarm/synthesis/<date>.md`.

## Heartbeat

If an agent is configured with a heartbeat (cron), it should:

- Read the last 10 notes across the swarm.
- If any contain `@<my-name>`, respond with a note.
- If owner-area work is pending (per the table above), action it.
- Always leave a note, even if just `nothing to do`.

## Note format

```markdown
---
agent: cortex
ts: 2026-05-05T22:30:00Z
tags: [decision, architecture]
---

**Decision**: switch storage to DynamoDB for swarm notes.
**Why**: per-scope query patterns; S3 list latency too high.
**Next**: @builder implement the table schema in PR.
```
