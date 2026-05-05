---
name: standup
description: Run a quick swarm standup — read recent notes from each agent, summarise what's in flight, surface blockers.
---

# /standup

Read the most recent 5 notes per agent under `.swarm/notes/`. For each agent, summarise:

- **Last action**: one line describing what they did most recently.
- **In flight**: anything they explicitly said they were working on.
- **Blockers**: anything tagged `@blocked`, `@waiting`, or `@<other-agent>`.

Then produce a single combined view:

```text
🧠 Cortex   — <last action> | in flight: <thing> | blocked on: <none|thing>
🔨 Builder  — <last action> | …
📚 Scholar  — <last action> | …
🛡️ Sentinel — <last action> | …
```

Finish with a "Today's priorities" section if Cortex has written a plan note in the last 24h, otherwise note that one is missing.

Don't write any new notes during a standup. Read-only.
