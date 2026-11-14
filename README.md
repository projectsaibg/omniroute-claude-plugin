# OmniRoute MCP Plugin for Claude Code and Kimi Code

Registers [OmniRoute](https://github.com/diegosouzapw/OmniRoute)'s MCP server with Claude
Code or Kimi Code, so the agent gets OmniRoute's full toolset (routing, providers, combos,
cache, compression, memory) as MCP tools -- without having to add the MCP server by hand.

Each integration does exactly one thing: it wires up the MCP connection. It does not bundle
any slash commands, agents, hooks, or skills. All of OmniRoute's own functionality (dashboard,
CLI, `omniroute run` / `omniroute configure`) is unaffected and still works the normal way.

## Prerequisites

- An OmniRoute instance already installed and running:
  ```bash
  npm install -g omniroute
  omniroute
  ```
  By default it listens on `http://localhost:20128` and exposes its MCP server at
  `http://localhost:20128/api/mcp/stream`.
- Claude Code or Kimi Code with plugin support.

## Install in Claude Code

### Option A: local testing (no marketplace)

Point Claude Code straight at this directory:

```bash
claude --plugin-dir /path/to/omniroute-claude-plugin
```

### Option B: as a marketplace

This repo is self-hosting: its `.claude-plugin/marketplace.json` lists the plugin at the
repo root. Once pushed to a Git host:

```
/plugin marketplace add <this-repo-url>
/plugin install omniroute@omniroute-claude-plugin
```

## Install in Kimi Code

The root `kimi.plugin.json` declares the same MCP connection for Kimi Code.

1. Start OmniRoute and enable MCP with the Streamable HTTP transport.
2. If your instance requires authentication, create an API key with the scopes your agent
   needs and expose it to Kimi Code without writing it into the repository:

   ```bash
   export OMNIROUTE_API_KEY="your-omniroute-key"
   ```

   On Windows PowerShell, use `$env:OMNIROUTE_API_KEY="your-omniroute-key"` before starting
   Kimi Code. For a non-loopback remote OmniRoute, the key also needs the `manage` scope.
3. In Kimi Code, install directly from GitHub and reload:

   ```text
   /plugins install https://github.com/projectsaibg/omniroute-claude-plugin
   /reload
   ```

4. Run `/mcp` to check the `omniroute` connection. Then ask the agent to call
   `omniroute_get_health` or `omniroute_list_combos` to verify real tool access.

## Configuration

The MCP server URL is hardcoded to `http://localhost:20128/api/mcp/stream`, matching
OmniRoute's own default port and the exact setup its README recommends for Claude Code.

If your OmniRoute instance runs on a different host/port, edit the `url` in both
[`.claude-plugin/plugin.json`](.claude-plugin/plugin.json) and
[`kimi.plugin.json`](kimi.plugin.json) before installation. Claude Code accepts an optional
`Authorization: Bearer <token>` header in its manifest. The Kimi Code manifest instead reads
the bearer token from `OMNIROUTE_API_KEY`, so the secret stays outside the repository.

## Verifying it works

After enabling the plugin, ask the agent to list its available MCP tools -- you should
see OmniRoute's tools (routing, providers, combos, etc.) alongside any others you have
configured.

## Notes

- OmniRoute is a separate, independently maintained project (MIT-licensed,
  `diegosouzapw/OmniRoute`). This plugin is not affiliated with it; it only points Claude
  Code or Kimi Code at an OmniRoute instance you already run.
- This repository configures local MCP clients. The hosted Kimi web custom-plugin runtime
  cannot reach your `localhost` instance, and OmniRoute's Bearer/API-key MCP authentication
  is not an OAuth connection.
