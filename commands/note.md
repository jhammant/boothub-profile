Write a swarm note from the current conversation context.

Pull from the most recent action you took (file edit, decision, finding) and produce a structured note. Don't ask the user to dictate — infer from what just happened.

1. Determine your agent identity from `AGENTS.md` (use the persona currently loaded, default to `unknown` if none).
2. Pick 1–3 tags from: `decision`, `code`, `research`, `audit`, `bug`, `ship`, `blocked`, `handoff`.
3. Write to `.swarm/notes/<agent>/<iso-timestamp>.md` using the format in `AGENTS.md`.
4. Commit: `git add .swarm && git commit -m "<agent>: <first line summary>"`.
