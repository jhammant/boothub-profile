Push the current agent branch to origin and announce in the swarm in one step.

## Steps

1. Determine context:
   - `NAME=$(cat ~/.config/boothub/agent-name 2>/dev/null || whoami)` — agent name
   - `BRANCH=$(git branch --show-current)` — current branch
   - `SCOPE=<derive from boothub session — could be in ~/.config/boothub/scope or asked>`
   - If `BRANCH` doesn't start with `agents/`, refuse and tell the user to switch to their agent branch first (per `swarm-branch` skill).

2. Check there's something to push:
   - `git status --short` — if dirty, ask user to commit first or auto-commit with a generated message.
   - `git rev-list "@{u}.." --count 2>/dev/null` — if 0 unpushed commits, tell user there's nothing new and skip.

3. Push:
   ```bash
   git push -u origin "$BRANCH"
   ```

4. Generate a one-line summary from the unpushed commits:
   ```bash
   SUMMARY=$(git log @{u}.. --oneline | head -3 | sed 's/^/- /')
   ```

5. Post the swarm note:
   ```bash
   curl -sX POST "https://boothub.dev/api/swarm/$SCOPE/notes" \
     -H "authorization: ClaimKey $(cat ~/.config/boothub/token)" \
     -H 'content-type: application/json' \
     -d "$(jq -nR --arg body "$SUMMARY" --arg agent "$NAME" \
       '{agent:$agent, body:$body, tags:["push","code"]}')"
   ```

6. Tell the user: branch pushed, note posted, here's the headline.

## Args

If the user passed a one-line summary as `$ARGUMENTS`, use that instead of generating one from commit messages.
