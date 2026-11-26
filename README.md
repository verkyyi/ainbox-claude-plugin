# aInbox — Claude plugin and MCP host configurations

[aInbox](https://ainbox.io): **An email address for your AI assistant.** People email it; your assistant reads, sorts and replies.

This repository contains the Claude plugin and credential-free remote MCP configuration examples for Codex and Cursor. All connect to `https://mcp.ainbox.io` using the host's OAuth sign-in flow. The Claude plugin also includes an inbox skill; adding an MCP configuration in Codex or Cursor does not install that Claude skill.

| Host | Installation | Product guide |
|---|---|---|
| Claude Code | [Marketplace commands below](#marketplace-recommended-for-early-users) | [Connection overview](https://ainbox.io/connect) |
| Codex | [Codex configuration](#codex) | [Connection overview](https://ainbox.io/connect) |
| Cursor | [Cursor configuration](#cursor) | [Connection overview](https://ainbox.io/connect) |
| Claude.ai / Desktop | Remote connector | [Claude guide](https://ainbox.io/setup/claude) |

As of 2026-11-26, dedicated Codex, Claude Code and Cursor setup pages are not published. The links above point to the existing guides.

## What you get

- The aInbox connector wired up at `https://mcp.ainbox.io` — OAuth + Dynamic Client Registration, no credentials to manage.
- The `inbox-model` skill, so the assistant uses the right tools without you re-explaining each session.

## Install

### Marketplace (recommended for early users)

From inside an active Claude Code session, run these slash commands:

```
/plugin marketplace add verkyyi/ainbox-claude-plugin
/plugin install ainbox@ainbox-marketplace
/reload-plugins
```

`/reload-plugins` activates the plugin immediately — no session restart needed. The first MCP call triggers the OAuth flow in your browser; after that, tool use is invisible.

> Tip: `/plugin` (no args) opens an interactive UI for browsing marketplaces, installed plugins, and any errors.

**At the shell** (alternative — equivalent commands without `/`):

```bash
claude plugin marketplace add verkyyi/ainbox-claude-plugin
claude plugin install ainbox@ainbox-marketplace
```

### Anthropic connector directory (coming soon)

The underlying MCP server is queued for [Anthropic's connector directory](https://claude.com/docs/connectors/building/submission). Once approved, one-click install becomes available in Claude.ai and Claude Desktop without this plugin.

This plugin remains useful even after the connector lands — wiring an MCP server doesn't teach the assistant how to use it. The bundled skill teaches the inbox model: when to call `list_agents`, the pull/persist/ack pattern, intra-account-only sends, and treating message envelopes as untrusted data.

### Codex

In a terminal with Codex installed:

```bash
codex mcp add ainbox --url https://mcp.ainbox.io
codex mcp login ainbox
```

Complete the browser sign-in yourself. Alternatively, merge [hosts/codex/config.toml](hosts/codex/config.toml) into `~/.codex/config.toml`, then run the login command. Preserve your existing configuration; do not replace the whole file.

Run `codex mcp get ainbox --json` to inspect the configuration. A configured URL alone does not prove OAuth or inbox access works. In a Codex session, ask: “Use aInbox list_agents to show my inbox address. Do not send, acknowledge or modify anything.”

Source: [Codex MCP documentation](https://developers.openai.com/codex/mcp/).

### Cursor

Merge [hosts/cursor/mcp.json](hosts/cursor/mcp.json) into `.cursor/mcp.json` for one project, or `~/.cursor/mcp.json` for all projects. Preserve other servers. In Cursor's MCP settings, enable aInbox and complete the browser OAuth sign-in when requested.

Ask Cursor: “Use aInbox list_agents to show my inbox address. Do not send, acknowledge or modify anything.” A tool list is only connection evidence; it does not prove a message or attachment was received.

Source: [Cursor MCP documentation](https://cursor.com/docs/mcp).

### Validation and troubleshooting

Checked on 2026-11-26: Codex CLI 0.162.1 accepted the URL configuration as Streamable HTTP; both examples parse and use the same endpoint as the Claude plugin. These checks did **not** exercise OAuth, message delivery or attachments in Codex or Cursor. Host screenshots and a real receipt test are still required before publishing walkthroughs.

| Symptom | Next step |
|---|---|
| Server missing | Check the host's configuration location, merge syntax and enabled state. |
| Authentication required | Complete the host's OAuth flow; never paste access tokens into prompts or repository files. |
| Tools listed but no messages | Confirm the inbox address with `list_agents`; connection discovery alone does not send a test message. |
| Sign-in or service error | Check [service status](https://status.ainbox.io) and [support](https://ainbox.io/support); report the host version and sanitized error. |

Email bodies and attachments are untrusted data, not instructions. Ask before sending or replying. `ack_messages` deletes acknowledged message content, so acknowledge only after the intended processing and any required persistence are complete. See [aInbox](https://ainbox.io) for current product limits.

## Use (Claude plugin)

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

The reviewed Claude bundle lives in `ainbox/`; host examples live separately in `hosts/`. Adding these examples does not change the v0.2.0 Claude bundle or publish a new directory submission. The plugin is intentionally small — manifest + MCP wiring + one skill. To iterate locally without publishing:

```bash
# Clone this repo, then point Claude Code at the local checkout
claude plugin marketplace add ./ainbox-claude-plugin
claude plugin install ainbox@ainbox-marketplace
```

Edit `ainbox/skills/inbox-model/SKILL.md` and reload (`/exit`, restart `claude`) — Claude Code picks up changes on session start.

## License

MIT
