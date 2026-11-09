# ai

Plugins for AI coding agents: language servers, MCP servers and skills, packaged so agent tools can install them in one step.

## Compatibility

| Tool | Status |
| --- | --- |
| Claude Code | Supported. Manifests are included. |
| Other agents (Codex, GitHub Copilot, Cursor, …) | Not yet packaged. The contents are mostly portable, but each tool needs its own manifest files. |

What carries over to other tools:

- **MCP servers** use the open [Model Context Protocol](https://modelcontextprotocol.io), which most AI coding tools support. Only the config file differs per tool.
- **Language servers** are standard LSP servers. The `.lsp.json` wiring is Claude Code's format, so other tools need their own LSP config, if they support LSP at all.
- **Skills** (`SKILL.md`) follow the open [agentskills.io](https://agentskills.io) standard.

Supporting another tool means adding its manifest next to the existing ones; the plugin contents stay shared. Microsoft's [dotnet/skills](https://github.com/dotnet/skills) is a working example of one repo serving Claude Code, Copilot, Cursor and Codex.

## Install

### Claude Code

```
/plugin marketplace add cardasac/ai
/plugin install astro@cardasac
```

Or from a local clone: `/plugin marketplace add /path/to/clone`. Start a new session (or run `/reload-plugins`) after installing.

## Plugins

| Plugin | What it does |
| --- | --- |
| [astro](plugins/astro) | Code intelligence for `.astro` files plus the Astro docs MCP server |

### astro

- **LSP** (`.lsp.json`): runs `@astrojs/language-server` from the **current project's** `node_modules`, so nothing is installed globally. The project needs `@astrojs/language-server` (pulled in by `@astrojs/check`) and `typescript`. `.ts` files are not handled here; use a TypeScript language server for those.
- **MCP** (`.mcp.json`): the official Astro docs server at `https://mcp.docs.astro.build/mcp`, for searching current Astro documentation. If you already added it to your tool separately, remove that entry to avoid a duplicate.

## Companion plugins

Official plugins from other marketplaces that I use alongside these.

| Plugin | Marketplace | Provides | Needs on `PATH` |
| --- | --- | --- | --- |
| `typescript-lsp` | `claude-plugins-official` (Anthropic) | TypeScript / JavaScript language server | `typescript-language-server`: `npm install -g typescript-language-server typescript` |
| `pyright-lsp` | `claude-plugins-official` (Anthropic) | Python language server (Pyright) | `pyright-langserver`: `npm install -g pyright` or `pipx install pyright` |
| `dotnet` | `dotnet-agent-skills` ([dotnet/skills](https://github.com/dotnet/skills), Microsoft) | Microsoft's Roslyn C# language server for `.cs`, `.razor`, `.cshtml`, plus .NET skills | .NET 10+ SDK (`dotnet dnx`) |

```
/plugin install typescript-lsp@claude-plugins-official
/plugin install pyright-lsp@claude-plugins-official
/plugin marketplace add dotnet/skills
/plugin install dotnet@dotnet-agent-skills
```

Do not also install `csharp-lsp` from Anthropic's marketplace. It runs the community `csharp-ls` server, and only one server handles each file extension ([plugin components](https://code.claude.com/docs/en/plugins/components)).

## Backup of my Claude Code setup

My settings, global instructions and MCP servers are in [config/claude-code](config/claude-code), with restore steps.

## Layout

```
config/claude-code/                 # backup of my ~/.claude config
.claude-plugin/marketplace.json     # Claude Code marketplace manifest
plugins/<name>/
  .claude-plugin/plugin.json        # Claude Code plugin manifest
  .mcp.json                         # MCP servers
  .lsp.json                         # language servers (Claude Code format)
  skills/<skill>/SKILL.md           # skills (agentskills.io)
```

## Adding a plugin

1. Create `plugins/<name>/.claude-plugin/plugin.json` with at least `{ "name": "<name>" }` (kebab-case).
2. Add components at the plugin root, not inside `.claude-plugin/` (e.g. `.mcp.json`, `.lsp.json`, `skills/`).
3. Register it in `.claude-plugin/marketplace.json` under `plugins`, with `source: "./plugins/<name>"`.
4. In Claude Code, run `/reload-plugins`, then `/plugin install <name>@cardasac`.

`claude plugin validate` does not check `.lsp.json`; LSP config errors show up in the `/plugin` **Errors** tab.
