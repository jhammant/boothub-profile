---
boothub: 1
profile: jhammant
source: https://github.com/jhammant/boothub-profile
targets:
  - claude-code
  - cursor
  - codex
summary: |
  jhammant's boothub profile. Bootstrap an agent with skills, slash
  commands, project context, and (optionally) a coordinated swarm of
  named agents.

  Pick a preset:
  • /jhammant            → default (skills + commands + CLAUDE.md + AGENTS.md)
  • /jhammant/lite       → just slash commands
  • /jhammant/swarm      → default + GitHub-as-shared-memory swarm bundle
  • /jhammant/swarm-pro  → default + hosted swarm (boothub.dev API) +
                            claw-bridge + claude-history MCPs

presets:
  default: [skills, commands, project-context]
  lite: [commands]
  swarm: [skills, commands, project-context, github-memory]
  swarm-pro:
    - skills
    - commands
    - project-context
    - github-memory
    - hosted-swarm
    - openclaw-personas
    - claw-bridge
    - claude-history

bundles:
  - id: skills
    kind: files
    only-on: claude-code
    files:
      - { path: .claude/skills/standup.md, from: skills/standup.md }
      - { path: .claude/skills/handoff.md, from: skills/handoff.md }

  - id: commands
    kind: files
    files:
      - { path: .claude/commands/standup.md, from: commands/standup.md }
      - { path: .claude/commands/synthesize.md, from: commands/synthesize.md }
      - { path: .claude/commands/note.md, from: commands/note.md }
    post: "Try /standup to see the swarm coordination protocol in action."

  - id: project-context
    kind: files
    files:
      - { path: CLAUDE.md, from: project/CLAUDE.md }
      - { path: AGENTS.md, from: agents/AGENTS.md }

  - id: openclaw-personas
    kind: files
    files:
      - { path: .claude/agents/cortex/SOUL.md, from: agents/cortex/SOUL.md }
      - { path: .claude/agents/builder/SOUL.md, from: agents/builder/SOUL.md }
      - { path: .claude/agents/scholar/SOUL.md, from: agents/scholar/SOUL.md }
      - { path: .claude/agents/sentinel/SOUL.md, from: agents/sentinel/SOUL.md }
    post: "Each agent has its own persona — see .claude/agents/*/SOUL.md."

  - id: github-memory
    kind: files
    files:
      - { path: .claude/skills/swarm-read.md, from: swarm/github-memory/swarm-read.md }
      - { path: .claude/skills/swarm-write.md, from: swarm/github-memory/swarm-write.md }
      - { path: .swarm/README.md, from: swarm/github-memory/SWARM-README.md }
    post: "Agents will read .swarm/notes/ before acting and write notes after. Commits = audit trail."

  - id: hosted-swarm
    kind: mcp
    mcp:
      claude-code:
        scope: user
        config:
          command: npx
          args: ["-y", "@boothub/swarm-mcp"]
      cursor:
        config:
          command: npx
          args: ["-y", "@boothub/swarm-mcp"]
    env_required:
      - BOOTHUB_TOKEN
    post: "Run `npx @boothub/swarm-mcp login` once to get a token, then it's saved at ~/.config/boothub/token."

  - id: claw-bridge
    kind: mcp
    only-on:
      - claude-code
      - cursor
    mcp:
      claude-code:
        scope: user
        config:
          command: node
          args:
            - "{{HOME}}/dev/claw-bridge/mcp-bridge/index.js"
      cursor:
        config:
          command: node
          args:
            - "{{HOME}}/dev/claw-bridge/mcp-bridge/index.js"
    template_vars:
      - HOME
    post: "Verify with /bridge_status."

  - id: claude-history
    kind: mcp
    only-on: claude-code
    mcp:
      claude-code:
        scope: user
        config:
          command: node
          args:
            - "{{HOME}}/dev/ClaudeHistoryMCP/dist/index.js"
    template_vars:
      - HOME
    env_required:
      - CLAUDE_HISTORY_CLOUD_TOKEN
    secrets:
      CLAUDE_HISTORY_CLOUD_TOKEN: |
        -----BEGIN AGE ENCRYPTED FILE-----
        YWdlLWVuY3J5cHRpb24ub3JnL3YxCi0+IFgyNTUxOSBmM25nT2RPMGJBaTRPZXdr
        OEgzRksvcTlxNDFUcDI5T2dsYkwvYnUraVhZCnZiYk1WWG1lQ3ZZNXZrRjdIZ1FR
        cW1EaTN1SEdpaVJCbE9reGpPNWJCekEKLS0tIDEweEVUU0JnR2o3bFVQM3h4Q09I
        bGZ5UWFDRWFLd1ZabDdCaThFaHlzVnMKyzTAd3nptS8o+FLJbogRKsqvfPF6RFQm
        6ryMwmw7HRMA+b626RjnAI6QxhSSqTpgozeLyV61QvdnxDL12jSmkJIN6w==
        -----END AGE ENCRYPTED FILE-----
    post: "If you have my age private key, the secret decrypts. Otherwise set CLAUDE_HISTORY_CLOUD_TOKEN manually."

post_install:
  - "Run /standup to see the swarm coordination protocol in action."
  - "If you used /swarm, your agents will start writing to .swarm/notes/."
  - "If you used /swarm-pro, run `npx @boothub/swarm-mcp login` to get a token."
---

# jhammant's boothub profile

This will install (depending on the preset you fetched):

- **Skills** — `swarm-read`, `swarm-write`, `standup`, `handoff` for working in a multi-agent setup
- **Slash commands** — `/standup`, `/synthesize`, `/note`
- **Project context** — `CLAUDE.md` + `AGENTS.md` describing how the agents coordinate
- **Agent personas** — Cortex (strategy), Builder (implementation), Scholar (research), Sentinel (security) — each with a SOUL.md
- **Swarm coordination** — either GitHub-backed (zero infra, every commit = audit trail) or hosted (DynamoDB-backed via boothub.dev's API)
- **MCPs** — `claw-bridge` for distributed task brokering, `claude-history` for cross-session memory

The bootstrapping agent will show you a diff and ask before writing each bundle. Skip any you don't want.
