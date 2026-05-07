---
name: swarm-branch
description: Convention for working on a per-agent git branch in a multi-agent boothub swarm. Read before starting work in any project that has AGENTS.md describing agent branches.
---

# Per-agent branch convention (async swarm)

When multiple agents are collaborating on the same repo, each one works on
its own branch named `agents/<agent-name>/main`. This avoids edit conflicts,
keeps history attributable, and makes "what did you work on?" trivially
answerable via `git log agents/<their-name>/main`.

## Decide your agent name

If the user gave you one (e.g. "you're playing the Builder role"), use that.
Otherwise default to your platform shortname: `claude`, `cursor`, `codex`,
plus a suffix if needed for uniqueness in this swarm (`claude-2`).

Save it for re-use:
```bash
echo "<your-name>" > ~/.config/boothub/agent-name
```

## On joining a project

Before doing any work:

```bash
# Are we already on an agents/ branch?
CURRENT=$(git branch --show-current)
case "$CURRENT" in
  agents/*) echo "✓ already on $CURRENT" ;;
  *) echo "⚠ on $CURRENT — switch to agents/<your-name>/main before editing" ;;
esac
```

If not on an agent branch, create one:
```bash
NAME=$(cat ~/.config/boothub/agent-name 2>/dev/null || echo "claude")
git fetch origin
git checkout -b "agents/$NAME/main" origin/main || git checkout "agents/$NAME/main"
```

## Push + announce after every meaningful commit

Use `/wt-push <one-line summary>` (preferred — does both git push + swarm
note in one step) OR manually:

```bash
git push -u origin "agents/$NAME/main"
curl -sX POST "https://boothub.dev/api/swarm/$SCOPE/notes" \
  -H "authorization: ClaimKey $(cat ~/.config/boothub/token)" \
  -H 'content-type: application/json' \
  -d "$(jq -nR --arg body "$SUMMARY" --arg agent "$NAME" \
    '{agent:$agent, body:$body, tags:["push","code"]}')"
```

## Pull peer branches when curious

`/wt-pull` (preferred) OR manually:

```bash
git fetch origin 'refs/heads/agents/*:refs/heads/agents/*'
git log --all --oneline --branches='agents/*' --since='2 hours ago'
```

To deeply inspect a peer's tree, add a temporary worktree:
```bash
git worktree add /tmp/peek-bob agents/bob/main
# look around
git worktree remove /tmp/peek-bob
```

## When done

Open a PR back to main:
```bash
gh pr create --base main --head "agents/$NAME/main" \
  --title "agents/$NAME: <work summary>" --body "Closes work on …"
```

Then post a final swarm note tagged `["push","done"]`.

## NEVER

- Push directly to `main` while a swarm is active. Always go through your
  agent branch + PR.
- Force-push your own agent branch if peers may have based work on it.
- Edit another agent's branch without asking via swarm note first.
