# This folder moved

The Gymmy Agent Plugin is installed from the **repository root**, which is what Cursor expects:

- [`plugin.json`](../plugin.json) — name `gymmy`, including the ChatGPT fields under `extensions.com.openai`
- [`mcp.json`](../mcp.json) — `mcpServers.gymmy` (`type: streamable-http`)
- [`skills/gymmy/SKILL.md`](../skills/gymmy/SKILL.md)
- [`assets/icon.png`](../assets/icon.png) and [`assets/logo.png`](../assets/logo.png) — the paths `plugin.json` already uses (`./assets/icon.png`, `./assets/logo.png`)

ChatGPT usage is unchanged. In ChatGPT: Settings → Apps → Advanced → Developer mode → **Create app**, URL `https://hellogymmy.com/api/mcp`, OAuth authentication. That URL is the hosted server; it does not depend on this directory.

Do not add a second `plugin.json` here. One manifest, at the repository root, keeps Cursor and ChatGPT on the same package.
