# Askaboard agent plugins (superseded)

These plugins were for the Askaboard 0.1 app. That app is replaced, and its
stdio relay (`~/.askaboard/bin/askaboard`), the `.mcpb` extension and the
board news hook no longer work. Do not install these plugins.

Askaboard now has an MCP server that any agent connects to directly. You do
not need a plugin or a skill. The server gives the agent the brief and the
board guide itself.

## Connect your agent

### To the desktop app

Keep the Askaboard app open. It serves the board that is open now at
`http://127.0.0.1:47821/mcp`, with no token.

Claude Code:

```sh
claude mcp add --transport http askaboard http://127.0.0.1:47821/mcp
```

Other MCP clients (Codex, Cursor, VS Code and others) use the same URL as a
streamable HTTP server:

```json
{
  "mcpServers": {
    "askaboard": {
      "type": "http",
      "url": "http://127.0.0.1:47821/mcp"
    }
  }
}
```

### To the web app

Use `https://app.askaboard.com/mcp` as the server URL. The client opens a
sign-in page, and you allow the connection there. A client must support
remote MCP servers with OAuth sign-in.

## If you installed a plugin before

Remove the plugin and any server entry that you added by hand. Then connect
as above. Otherwise the agent can see every tool twice.

```sh
claude plugin uninstall askaboard@askaboard
claude mcp remove askaboard
codex mcp remove askaboard
```

## License

MIT
