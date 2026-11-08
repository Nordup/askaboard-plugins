# Askaboard agent plugins

Plugins that connect your AI agent to Askaboard, a shared canvas on your Mac or Linux computer. You talk with the agent, and it explains on the board with drawings and animations. You can also write and draw on the board; the agent sees it and checks your work.

Each plugin adds the `askaboard` MCP server and a small skill that teaches the agent how to work on the board. See [plugins/askaboard/README.md](plugins/askaboard/README.md) for what the plugin does and for example prompts.

## Before you install

1. Download Askaboard for Mac from the [GitHub releases of `Nordup/askaboard`](https://github.com/Nordup/askaboard/releases/latest).
2. Open the app once. The first launch writes `~/.askaboard/bin/askaboard`, the command that agents use to connect.
   On Linux, run the app from source: clone [`github.com/Nordup/askaboard`](https://github.com/Nordup/askaboard), go to the folder `space/` and run `npm install && npm run dev`.
3. Keep the app open while you work with the agent.

If you already added the server by hand (`claude mcp add askaboard …` or `codex mcp add askaboard …`), remove that entry first. Otherwise the agent sees every tool twice.

```sh
claude mcp remove askaboard
codex mcp remove askaboard
```

## Manual setup

The Askaboard app installs the plugins for you: click the agent pill, then **Connect an agent**. To do it by hand, use the steps for your agent.

### Claude Code

Covers the terminal and the Code tab in Claude Desktop.

```sh
claude plugin marketplace add Nordup/askaboard-plugins
claude plugin install askaboard@askaboard
```

Then restart Claude Code, or run `/reload-plugins`. Inside Claude Code, `/plugin marketplace add Nordup/askaboard-plugins` and `/plugin install askaboard@askaboard` do the same. On Claude Code 2.1.275 or later, one command does both: `/plugin install askaboard --marketplace Nordup/askaboard-plugins`.

### Board news hook

The Claude Code and Codex plugins run `~/.askaboard/bin/askaboard news --client "<agent>"` before each prompt (a `UserPromptSubmit` hook). When the learner changed something on the board since this agent app's last look or last board news, the command prints one line, for example `Board news for "Stars", since your last look or news 2 min ago: the learner drew 9 strokes: ink group g12 (310,-40 280×210) right of your svg "Thermal spectra". The last change was 40 s ago.` The agent gets this line with the prompt. Each change is in the news once: after the hook printed it, the app does not repeat it in tool results. When nothing is new or the app is closed, the command prints nothing.

To add the hook by hand (without the plugin), put this in `~/.codex/hooks.json` or `~/.claude/settings.json`. The example is for Codex; for Claude Code, use `"Claude Code"` as the client:

```json
{
  "hooks": {
    "UserPromptSubmit": [
      { "hooks": [{ "type": "command", "command": "\"$HOME/.askaboard/bin/askaboard\" news --client \"Codex\" 2>/dev/null || true", "timeout": 5 }] }
    ]
  }
}
```

### ChatGPT and Codex

The ChatGPT desktop app and Codex CLI share one setup.

```sh
codex plugin marketplace add Nordup/askaboard-plugins
codex plugin add askaboard@askaboard
```

Then restart the ChatGPT app or Codex. You can also install **Askaboard** from `/plugins` in Codex, or from Settings → Plugins in the ChatGPT app.

Codex runs a plugin's hooks only after you trust them: run `/hooks` in Codex and trust the Askaboard `UserPromptSubmit` hook. Without it, Codex works as before, but the board news does not reach your prompts.

### Claude Desktop (Chat tab)

1. Download [`askaboard.mcpb`](claude-extensions/askaboard/dist/askaboard.mcpb).
2. Double-click it, or drag it into Claude Desktop. On Linux, run `claude-desktop <path to askaboard.mcpb>`, or use **Install** in the app's agent list.

### Cursor

Add the GitHub repository `Nordup/askaboard-plugins` as a plugin marketplace in Cursor. Then install **Askaboard** from it. Cursor reads `.cursor-plugin/marketplace.json`.

### Other agents

For VS Code, Windsurf and other MCP clients. HTTP clients use the server URL; stdio clients use the relay command.

- Server URL (the app must be open): `http://127.0.0.1:47821/mcp`
- Relay command (stdio): `~/.askaboard/bin/askaboard mcp`

```json
{
  "mcpServers": {
    "askaboard": {
      "command": "~/.askaboard/bin/askaboard",
      "args": ["mcp"]
    }
  }
}
```

Some clients do not expand `~`. Use the full path, for example `/Users/<you>/.askaboard/bin/askaboard` on macOS or `/home/<you>/.askaboard/bin/askaboard` on Linux.

## Updates

When a new version is out:

- Claude Code: `claude plugin marketplace update askaboard`, or turn on auto-update for the marketplace in `/plugin`.
- Codex: `codex plugin marketplace upgrade`.
- Claude Desktop: download the new `.mcpb` and open it.

## Develop

Requires Node 20 or later and Info-ZIP `zip`.

```sh
npm install
npm run build          # version check, validation, rebuild the .mcpb
```

The root `package.json` `version` is the only version source. To release:

1. Change `version` in `package.json` and add a `CHANGELOG.md` entry.
2. Run `npm run version:sync`. It writes the version into every manifest and rebuilds the `.mcpb`.
3. Run `npm run build` and commit everything, including `claude-extensions/askaboard/dist/askaboard.mcpb`.

CI runs `npm run build` and `npm run check-artifact`. The second command fails when the committed `.mcpb` differs from a fresh build.

Do not add a `plugin.json` or `mcp.json` at the plugin root. It makes the plugin an Agent Plugin, and Codex then cuts each tool description and the server instructions at 1,000 bytes and skips the hooks. `npm run build` fails if one exists.

| Client | Manifest | MCP wiring |
| --- | --- | --- |
| Claude Code | `plugins/askaboard/.claude-plugin/plugin.json` | `mcp.claude.json` (stdio relay) |
| Codex | `plugins/askaboard/.codex-plugin/plugin.json` | `mcp.codex.json` (HTTP) |
| Cursor | `plugins/askaboard/.cursor-plugin/plugin.json` | `mcp.cursor.json` (stdio relay) |
| Claude Desktop | `claude-extensions/askaboard/manifest.json` | relay binary |

The stdio relay lists tools even when the app is closed. The HTTP wiring connects to `http://127.0.0.1:47821/mcp` and needs the app to be open.

## License

MIT
