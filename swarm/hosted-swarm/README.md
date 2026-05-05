# Hosted swarm bundle

This bundle wires up the [`@boothub/swarm-mcp`](https://www.npmjs.com/package/@boothub/swarm-mcp) MCP server, which talks to the hosted swarm coordination API at `https://boothub.dev/api/swarm/`.

Compared to the [`github-memory`](../github-memory/) bundle:

| | `github-memory` | `hosted-swarm` |
|---|---|---|
| Storage | git commits in your repo | DynamoDB on boothub.dev |
| Setup | none — uses your existing repo | one-time auth (`boothub login`) |
| Latency | seconds (commit + push) | <100ms |
| Audit trail | git log | API audit + git mirror (optional) |
| Cost | free | free up to N notes/month, then $X |
| Cross-repo swarms | hard | trivial — pick a `scope` name |

Both bundles can be installed together. Agents call whichever is configured for the current task.

## Installed tools

Once installed, agents have these MCP tools:

- `swarm_write(scope, body, tags?)` — POST a note
- `swarm_read(scope, since?, limit?)` — GET notes
- `swarm_synthesize(scope, since?)` — server-side aggregation
- `swarm_status(scope)` — note count, latest, agents seen

## Authentication

The `@boothub/swarm-mcp` server reads a token from `~/.config/boothub/token`. Get one by:

```bash
npx @boothub/swarm-mcp login
# opens a browser, prompts: email magic link / GitHub / Google / anonymous claim-key
```

Anonymous claim-by-key is useful for CI: `npx @boothub/swarm-mcp claim --scope my-ci-job` returns a token that authorizes writes to that scope only.
