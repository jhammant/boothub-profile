Synthesise the last $ARGUMENTS notes (default: 20) across all agents in `.swarm/notes/` into a single decision document.

1. List `.swarm/notes/` recursively, sort by timestamp, take the most recent N.
2. Group by topic (use note tags or infer from content).
3. For each topic produce: **Topic** → **What's been said** (one line each) → **Convergence** (what consensus, if any) → **Open question** (if any).
4. Write the result to `.swarm/synthesis/<iso-timestamp>.md`.
5. Commit: `git add .swarm/synthesis && git commit -m "synthesis: <topic count> topics from last N notes"`.

Be ruthless about noise. A synthesis is for humans to scan in 60 seconds.
