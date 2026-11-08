Askaboard is a shared canvas for learning on your Mac or Linux computer. You write and draw by hand. Your AI agent sees the board, draws diagrams, explains ideas next to your work and checks each step. There is no AI inside the app: the agent you already use connects to it over MCP.

**What the plugin adds**

- The `askaboard` MCP server, so the agent can read the board, take screenshots, write notes and diagrams, and draw ink.
- An `askaboard` skill that tells the agent to read the Askaboard brief (`get_brief`), where to find the board reference (`get_guide`, with scenes first: programs that draw and animate on the board), and what to do when the app is not running.

**Prerequisites**

- macOS or Linux.
- Askaboard opened once. On a Mac, download it from https://github.com/Nordup/askaboard/releases/latest. On Linux, run it from source (`github.com/Nordup/askaboard`, folder `space/`, `npm install && npm run dev`). The first launch writes the `~/.askaboard/bin/askaboard` command that agents use to connect.
- Keep the app open while you work. If it is closed, the agent asks you to open it.

**Install**

- Claude Code: `/plugin install askaboard --marketplace Nordup/askaboard-plugins`
- Codex: `codex plugin marketplace add Nordup/askaboard-plugins`, then install **Askaboard** from `/plugins`.
- Other clients: see https://github.com/Nordup/askaboard-plugins.

**Example prompts**

- "Look at my board in Askaboard and check my last step."
- "Teach me how derivatives work on the board, one small step at a time."
- "Give me a hint for the problem I wrote, but not the answer."
- "Draw a diagram of the unit circle next to my notes."
