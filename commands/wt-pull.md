Fetch all peer agent branches and summarise what's new.

## Steps

1. Fetch all branches matching `agents/*`:
   ```bash
   git fetch origin 'refs/heads/agents/*:refs/heads/agents/*' 2>&1
   ```

2. Identify peer branches (everything matching `agents/*` that isn't yours):
   ```bash
   NAME=$(cat ~/.config/boothub/agent-name 2>/dev/null || whoami)
   git for-each-ref --format='%(refname:short)' 'refs/heads/agents/*' | grep -v "agents/$NAME/"
   ```

3. For each peer branch, show recent commits since 2 hours ago:
   ```bash
   git log --oneline --since='2 hours ago' agents/<peer-name>/main
   ```

4. Also pull the latest swarm notes tagged `push` to correlate commits with announcements:
   ```bash
   curl -s "https://boothub.dev/api/swarm/$SCOPE/notes?limit=20" \
     -H "authorization: ClaimKey $(cat ~/.config/boothub/token)" | \
     jq '.notes[] | select(.tags | index("push"))'
   ```

5. Summarise for the user:
   - Which peers committed recently?
   - What did they say in their `push` notes?
   - Any commits with no announcement (silent push)?
   - Any branches that diverged from `main` significantly (might need merge)?

6. Optionally suggest follow-up:
   - "Want me to add a worktree at `/tmp/peek-<peer>` to inspect their tree?"
   - "Want me to rebase your branch onto a peer's work?"

Don't merge or modify anything without explicit user approval. This command is read-only.
