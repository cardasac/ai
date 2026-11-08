# claude-plugins

Personal [Claude Code](https://code.claude.com) plugin marketplace.

## Install

From a local clone:

```
/plugin marketplace add ~/development/github/claude-plugins
/plugin install astro@calexc
```

From GitHub (once pushed):

```
/plugin marketplace add <github-user>/claude-plugins
/plugin install astro@calexc
```

Start a new session (or run `/reload-plugins`) after installing.

## Plugins

| Plugin | What it does |
| --- | --- |
| [astro](plugins/astro) | Code intelligence for `.astro` files plus the Astro docs MCP server |

### astro

- **LSP** (`.lsp.json`): runs `@astrojs/language-server` from the **current project's** `node_modules`, so nothing is installed globally. The project needs `@astrojs/language-server` (pulled in by `@astrojs/check`) and `typescript`. `.ts` files are not handled here; use the official `typescript-lsp` plugin for those.
- **MCP** (`.mcp.json`): the official Astro docs server at `https://mcp.docs.astro.build/mcp`, for searching current Astro documentation. If you previously added it with `claude mcp add`, remove that entry to avoid a duplicate.

## Adding a plugin

1. Create `plugins/<name>/.claude-plugin/plugin.json` with at least `{ "name": "<name>" }` (kebab-case).
2. Add components at the plugin root, not inside `.claude-plugin/` (e.g. `.lsp.json`, `skills/`, `commands/`, `hooks/`).
3. Register it in `.claude-plugin/marketplace.json` under `plugins`, with `source: "./plugins/<name>"`.
4. Run `/reload-plugins`, then `/plugin install <name>@calexc`.

`claude plugin validate` does not check `.lsp.json`; LSP config errors show up in the `/plugin` **Errors** tab.
