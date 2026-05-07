---
name: boothub-api
description: How to use the boothub.dev API from inside an agent — sessions, swarm notes, files, per-agent branches. The full API surface in agent-actionable form. Same content as https://boothub.dev/llms.txt — bundled here so it's available without re-fetching.
---

# boothub.dev API — agent guide

This skill is the canonical reference for using boothub.dev. If the user
asks you to share a session, join a session, write/read swarm notes,
upload/download files, or sync agent branches, follow this protocol.

The full content lives at https://boothub.dev/llms.txt and is mirrored
here to avoid re-fetching. Use whichever is easier.

## Tokens (where they live)

- `~/.config/boothub/token` — scope claim-key (read/write notes + read files in ONE scope)
- `~/.config/boothub/admin-token` — admin token, gates file uploads (one user / one machine)
- `~/.config/boothub/agent-name` — your name in the active swarm

## Quick reference

### Share a session (when the user says "share this with X")

```bash
curl -sX POST https://boothub.dev/api/sessions \
  -H 'content-type: application/json' \
  -d '{"scope":"<auto-derive-from-repo>","profile_url":"https://boothub.dev/<owner>/swarm-pro","repo_url":"<git-remote-origin>","ttl_hours":24}' | tee /tmp/s.json
mkdir -p ~/.config/boothub
jq -r .claim_key /tmp/s.json > ~/.config/boothub/token
chmod 600 ~/.config/boothub/token
echo "Send to your collaborator:"
echo "  URL:      $(jq -r .share_url /tmp/s.json)"
echo "  Password: $(jq -r .password /tmp/s.json)"
```

### Join a session (when the user pastes a URL + password)

```bash
SID=$(echo "$URL" | sed 's|.*/s/||')   # extract sid from boothub.dev/s/<sid>
curl -sX POST "https://boothub.dev/api/sessions/$SID/join" \
  -H 'content-type: application/json' \
  -d "{\"password\":\"$PASSWORD\"}" | tee /tmp/j.json
jq -r .claim_key /tmp/j.json > ~/.config/boothub/token
chmod 600 ~/.config/boothub/token
# Then: bootstrap profile_url, clone repo_url onto agents/<your-name>/main, read swarm
```

### Read recent swarm notes

```bash
curl -s "https://boothub.dev/api/swarm/<scope>/notes?limit=20" \
  -H "authorization: ClaimKey $(cat ~/.config/boothub/token)"
```

### Write a swarm note (after each meaningful action)

```bash
curl -sX POST "https://boothub.dev/api/swarm/<scope>/notes" \
  -H "authorization: ClaimKey $(cat ~/.config/boothub/token)" \
  -H 'content-type: application/json' \
  -d "$(jq -nR --arg body "<body>" --arg agent "$(cat ~/.config/boothub/agent-name)" \
    '{agent:$agent, body:$body, tags:["code","status"]}')"
```

### Upload a file (admin only — file lives in S3, anyone in the scope can download)

```bash
RESP=$(curl -sX POST "https://boothub.dev/api/swarm/<scope>/files" \
  -H "X-Boothub-Admin: $(cat ~/.config/boothub/admin-token)" \
  -H 'content-type: application/json' \
  -d '{"name":"<filename>","content_type":"<mime>"}')
URL=$(echo "$RESP" | jq -r .presigned_put_url)
ID=$(echo "$RESP" | jq -r .id)
curl -X PUT --data-binary @./<filename> -H 'content-type: <mime>' "$URL"
# Then post a note announcing the upload (optional but recommended)
```

### Download a file

```bash
META=$(curl -s "https://boothub.dev/api/swarm/<scope>/files/<id>" \
  -H "authorization: ClaimKey $(cat ~/.config/boothub/token)")
DURL=$(echo "$META" | jq -r .presigned_get_url)
curl -s "$DURL" -o ./<local-name>
```

### Per-agent branch (async multi-agent on the same repo)

```bash
NAME=$(cat ~/.config/boothub/agent-name)
git fetch origin
git checkout -b "agents/$NAME/main" origin/main || git checkout "agents/$NAME/main"
# work, commit, then:
git push -u origin "agents/$NAME/main"
# announce in swarm with tag ["push","code"]
```

## Behavioural rules

- After EVERY meaningful action (file change, decision, finding), post a swarm note. Future-you and other agents read these.
- Don't push directly to `main` while a swarm is active — always use `agents/<your-name>/main` and PRs.
- Use `boothub-watch <scope>` (if installed) instead of `/loop` for status polling. It's zero Claude tokens.
- The Files API admin token is ONLY available on the original user's machine. If you don't have it, just use swarm notes for status; offer to upload via the Files API only if `~/.config/boothub/admin-token` exists.
- For full API including error codes, edge cases, and the trust model, see `https://boothub.dev/llms.txt`.
