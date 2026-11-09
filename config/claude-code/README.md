# Claude Code setup backup

A copy of my `~/.claude/` config so the setup can be restored on a new machine or after a reinstall.

Claude Code has no per-user sync for these files. Only plugins, skills and connectors turned on in the claude.ai account follow the login ([plugin loading](https://code.claude.com/docs/en/plugins/loading), [MCP](https://code.claude.com/docs/en/mcp)). Managed settings exist, but they are for organizations: server-managed settings need a Claude for Teams or Enterprise plan ([server-managed settings](https://code.claude.com/docs/en/server-managed-settings)).

## Rule: official plugins first

If a tool has an official plugin, use the plugin. No hand-added MCP servers (`claude mcp add`), no hand-copied skills in `~/.claude/skills/`, and no claude.ai connectors for anything a plugin already covers. Connectors are only for services that have no plugin.

Everything then lives in `settings.json`, so nothing in `~/.claude.json` needs backing up. `settings.json` also sets `syncClaudeAiPlugins` to `false`, so plugins turned on in the claude.ai account stay in the claude.ai apps and do not load in Claude Code ([settings](https://code.claude.com/docs/en/settings)).

## Files

| File | Copy to | What it is |
| --- | --- | --- |
| `settings.json` | `~/.claude/settings.json` | Permissions, enabled plugins, extra marketplaces, output style, model settings |
| `AGENTS.md` | `~/.claude/AGENTS.md` | Global instructions |

By default Claude Code reads `AGENTS.md` only when there is no `CLAUDE.md` in the working directory or above it ([memory](https://code.claude.com/docs/en/memory)). `~/.claude/AGENTS.md` is found as `.claude/AGENTS.md` in a directory above the project, so it covers projects inside the home directory. The backed up `settings.json` sets `pluginConfigs."cc-plugin-agents-md@builtin".options.instructionFiles` to `claude-md-and-agents-md` (the **Project instructions** option in `/config`), so it also loads in projects that have their own `CLAUDE.md`. This key is only read from user or managed settings, not project settings.

## Restore

1. Copy the files:

   ```
   cp settings.json AGENTS.md ~/.claude/
   ```

   On the next start, Claude Code clones the marketplaces in `extraKnownMarketplaces` and downloads the plugins in `enabledPlugins` that are not cached yet ([plugin loading](https://code.claude.com/docs/en/plugins/loading)). If one does not come back, reinstall it with `/plugin install <id>` using the IDs in `settings.json`.

2. Install the tools the plugins run. Taken from each plugin's README and its MCP, LSP or hook config:

   | Tool | Needed by |
   | --- | --- |
   | Node.js (`npm`, `npx`) | `typescript-lsp`, `pyright-lsp`, `playwright` (`npx @playwright/mcp`), `astro` (project `node_modules`), `chrome-devtools-mcp` (`npx chrome-devtools-mcp`) |
   | Chrome, current stable | `chrome-devtools-mcp` |
   | `go install golang.org/x/tools/gopls@latest` (with `$HOME/go/bin` in `PATH`) | `gopls-lsp` |
   | `npm install -g typescript-language-server typescript` | `typescript-lsp` |
   | `npm install -g pyright` | `pyright-lsp` |
   | .NET 10+ SDK (`dotnet dnx`) | `dotnet` (Roslyn C# language server), `dotnet-msbuild` (binlog MCP) |
   | `uv` (`uvx`) | `aws-core` (AWS MCP), `deploy-on-aws` (IaC and pricing MCPs) |
   | Python 3.9+ | `deploy-on-aws`, `security-guidance` |
   | `pip3 install defusedxml>=0.7.1` | `deploy-on-aws` (diagram validation) |
   | Dart / Flutter SDK | `dart-and-flutter` (`dart mcp-server`) |
   | Aspire CLI | `aspire` |
   | Docker or Podman | `sonarqube` (MCP server runs in a container) |
   | AWS CLI with credentials | `deploy-on-aws` (deployment skills), `aws-core` (authenticated AWS calls) |

3. Plugins that need sign-in or setup:

   - `github`: set `GITHUB_PERSONAL_ACCESS_TOKEN` in the shell Claude Code starts from, then start Claude Code from a new terminal. A fine-grained token covers one owner only; use a classic token (`repo`, `read:org`, `read:packages`) to cover more than one account or organization.
   - `context7`: sign in through `/mcp`. Optional: `CONTEXT7_API_KEY` for your own Context7 plan.
   - `sonarqube`: run `/sonarqube:sonar-integrate`. It installs `sonarqube-cli`, runs `sonar auth login` (token goes to the system keychain), then `sonar integrate claude`, which adds the SonarQube MCP server and secrets-scanning hooks to the Claude Code config. Do not copy those hooks into this backup; `sonar integrate claude` recreates them.
   - `aspire`: the Aspire MCP server is opt-in per repository with `aspire agent init --mcp`.

4. Sign in with the claude.ai account. Connectors added at claude.ai become available in Claude Code with a claude.ai subscription login ([MCP](https://code.claude.com/docs/en/mcp)).

## Not backed up

- `~/.claude.json`: account state, per-project history and secrets. Never commit it.
