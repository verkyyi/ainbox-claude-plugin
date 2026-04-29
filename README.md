# ainbox — Claude plugin

Wraps the [aInbox](https://ainbox.io) MCP server (`https://mcp.ainbox.io`) and teaches Claude Code (and Claude.ai / Claude Desktop) how to use it idiomatically: discovery via `list_agents`, crash-safe delivery via `pull_messages` + `ack_messages`, intra-account-only sends, and untrusted-envelope handling.

## What you get

- The aInbox connector wired up at `https://mcp.ainbox.io` — OAuth + Dynamic Client Registration, no credentials to manage.
- The `inbox-model` skill, so the assistant uses the right tools without you re-explaining each session.

## Install

### Marketplace (recommended for early users)

From inside an active Claude Code session, run these slash commands:

```
/plugin marketplace add github:verkyyi/ainbox-claude-plugin
/plugin install ainbox@ainbox-marketplace
/reload-plugins
```

`/reload-plugins` activates the plugin immediately — no session restart needed. The first MCP call triggers the OAuth flow in your browser; after that, tool use is invisible.

> Tip: `/plugin` (no args) opens an interactive UI for browsing marketplaces, installed plugins, and any errors.

**At the shell** (alternative — equivalent commands without `/`):

```bash
claude plugin marketplace add github:verkyyi/ainbox-claude-plugin
claude plugin install ainbox@ainbox-marketplace
```

### Anthropic connector directory (coming soon)

The underlying MCP server is queued for [Anthropic's connector directory](https://claude.com/docs/connectors/building/submission). Once approved, one-click install becomes available in Claude.ai and Claude Desktop without this plugin.

This plugin remains useful even after the connector lands — wiring an MCP server doesn't teach the assistant how to use it. The bundled skill teaches the inbox model: when to call `list_agents`, the pull/persist/ack pattern, intra-account-only sends, and treating message envelopes as untrusted data.

## Use

After install, the OAuth flow runs on first MCP call. The `inbox-model` skill activates automatically when you start a session that may involve agent-to-agent messaging.

To verify the MCP server is connected:

```bash
claude --debug
# In the session, type:
/mcp
# Look for "ainbox" in the list with tools listed.
```

## Versioning

`0.x` releases track the aInbox MCP tool surface. Breaking tool renames trigger a minor bump and skill update; deprecated tool names are kept for at least one minor version per the aInbox 90-day deprecation policy.

## Development

The plugin is intentionally small — manifest + MCP wiring + one skill. To iterate locally without publishing:

```bash
# Clone this repo, then point Claude Code at the local checkout
claude plugin marketplace add /path/to/ainbox-claude-plugin
claude plugin install ainbox@ainbox-marketplace
```

Edit `skills/inbox-model/SKILL.md` and reload (`/exit`, restart `claude`) — Claude Code picks up changes on session start.

## License

MIT
