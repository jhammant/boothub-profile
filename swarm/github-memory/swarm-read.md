---
name: swarm-read
description: Read recent swarm notes from .swarm/notes/ before starting any task.
---

# Read the swarm before you act

Before starting any non-trivial task, run this protocol:

1. List `.swarm/notes/` recursively. If empty or missing, write a note that you're starting fresh and skip the rest.
2. Sort entries by timestamp (filename), take the most recent 5 across all agents and the most recent 5 from your own subdirectory.
3. Read them.
4. Look for:
   - Outstanding `@<your-name>` mentions you should respond to.
   - Decisions that constrain your work.
   - Recent changes you might step on.
5. If you find blockers or ambiguity, write a question note (do not silently guess).

Don't skip this even when the user says "just do X." The swarm exists so agents don't duplicate work or contradict each other.
