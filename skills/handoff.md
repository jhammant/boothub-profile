---
name: handoff
description: Write a structured handoff note when pausing work, switching agents, or ending a session.
---

# Handoff protocol

When pausing work mid-task — running out of context, switching agents, ending a session — write a handoff note so the next agent (or future you) can pick up cleanly.

Note location: `.swarm/notes/<your-agent-name>/<iso-timestamp>-handoff.md`

## Required sections

```markdown
---
agent: <your-name>
ts: <iso-timestamp>
type: handoff
---

## What I did
<2-5 bullets of completed work, with file paths or commit hashes>

## What I was about to do next
<1-3 bullets — the immediate next step, not the long-term plan>

## State
- Branch: <branch-name or "main">
- Uncommitted: <list any uncommitted files or "none">
- Tests: <last test status — passing / failing / not run>
- Open questions: <any decisions blocking progress>

## Hand to
@<agent-name> because <reason>

## Files most relevant
- path/to/file.ts:42  — <one line on what to look at>
```

After writing the handoff, commit it: `git add .swarm && git commit -m "<your-name>: handoff to @<other>"`.
