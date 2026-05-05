# Project context

This file is dropped into the project root by the boothub default preset. Customise it for your project; the version below is the starter content.

## Working style

- Make a plan before non-trivial changes; write it as a note in `.swarm/notes/<agent>/`.
- Prefer small, atomic commits with conventional commit messages.
- Run tests before claiming work is done. If you can't run tests, say so.
- When uncertain about direction, write a question note tagged `@<another-agent>` rather than guess.

## Conventions

- TypeScript strict mode; never `any` unless explicitly justified.
- Tests in `tests/` mirroring source structure.
- `npm test` runs vitest. `npm run typecheck` runs tsc --noEmit.
- Follow existing patterns in adjacent files; don't introduce new abstractions for one-offs.

## Multi-agent

This project uses a swarm. Read `AGENTS.md` for the roster and coordination protocol. Always identify yourself before writing to `.swarm/notes/`.
