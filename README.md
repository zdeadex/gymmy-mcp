<p align="center"><img src="assets/icon-512.png" alt="Gymmy" width="96" height="96"></p>

# Gymmy MCP server

[![Gymmy MCP on mcprush](https://mcprush.com/badge/gymmy-mcp/score.svg)](https://mcprush.com/hellogymmy/gymmy-mcp)

Official [Model Context Protocol](https://modelcontextprotocol.io) server for [Gymmy](https://hellogymmy.com): find gyms and studios, check schedules and prices, drop in on a class, join a gym, book with your membership, and start a free trial from any AI assistant.

Gymmy is the booking and management platform of independent gyms and studios in France and Europe (CrossFit, HYROX, fitness, yoga, pilates, boxing, martial arts, dance). Ask your assistant to find a gym near you, see its class schedule and prices, and get a trial class link. Connect your Gymmy account to see your bookings, book a class with your membership or cancel one, under your gym's own rules. Gyms not on Gymmy yet come from OpenStreetMap and are clearly marked as not bookable.

A drop-in (trial, single class, or class pack) or a membership is paid only on the Stripe Checkout page the assistant returns. No card data reaches the chat.

This is a **hosted remote server**: nothing to install or run. The same repository is also an [Agent Plugin](https://agent-plugins.org/specification) that Cursor installs from this root. The plugin name and the MCP server key are both `gymmy`.

| | |
| --- | --- |
| MCP URL | `https://hellogymmy.com/api/mcp` |
| MCP URL, sign-in at connect (Claude) | `https://hellogymmy.com/api/mcp/member` |
| Transport | Streamable HTTP (stateless) |
| Auth | OAuth 2.1 + PKCE (S256), Client ID Metadata Documents or Dynamic Client Registration, RFC 8707 resource indicators. Public tools need no sign-in. |
| Plugin name | `gymmy` |
| Registry name | `com.hellogymmy/gymmy` |

[![Add to Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/en/install-mcp?name=gymmy&config=eyJ1cmwiOiJodHRwczovL2hlbGxvZ3ltbXkuY29tL2FwaS9tY3AifQ==)

## Tools

| Tool | Sign-in | What |
| --- | --- | --- |
| `search_gyms` | no | Find gyms by name, place or coordinates. Gymmy gyms are bookable; OpenStreetMap listings are flagged not on Gymmy |
| `get_gym` | no | Description, address, hours, sites, how to book |
| `get_schedule` | no | Next 14 days of classes, with a link per class |
| `get_prices` | no | Offers with EUR prices and a sign-up link each |
| `start_trial` | no | Trial class link (offer and class picked). Books nothing and charges nothing |
| `search_classes` | no | Classes open to drop-in visitors (trial, single class, or class pack), with price and offer ids |
| `book_dropin` | yes | Buy a trial, single class, or class pack and book it. The first call returns a summary; `confirm: true` only after an explicit yes, then the gym's Stripe link (or a booking when free) |
| `join_gym` | yes | Membership or class pack from `get_prices`. Same confirm-then-Stripe pattern, with no class |
| `checkout_status` | yes | `pending`, `processing`, `paid`, `free`, or `failed` for a `book_dropin` or `join_gym` ref |
| `my_gyms` | yes | Gyms of the signed-in member |
| `my_bookings` | yes | Upcoming booked and waitlisted classes |
| `my_schedule` | yes | Week of classes with class ids |
| `book_class` | yes | Books with the member's own membership (gym rules apply, no payment). The assistant asks before it calls this tool |
| `cancel_booking` | yes | Cancels a booking (the gym's cancel deadline applies). The assistant asks before it calls this tool |

Every tool has a `title` and `readOnlyHint` / `destructiveHint` annotations. Calling a member tool without signing in starts the OAuth sign-in.

`book_dropin` and `join_gym` do not charge on the first call. Payment happens only on the Stripe link returned after `confirm: true`. `book_class` and `cancel_booking` have no `confirm` argument; the assistant still asks you before it calls them.

## Setup

### Cursor

**Agent Plugin** (skill + MCP server). Cursor loads this repository from its root: [`plugin.json`](plugin.json), [`mcp.json`](mcp.json), and [`skills/gymmy/SKILL.md`](skills/gymmy/SKILL.md). In `mcp.json` the server key is `gymmy` (`type: streamable-http`).

- Marketplace: once the plugin is listed, install **gymmy** from Customize. Submit this repository at [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish).
- Team marketplace: Dashboard → Plugins & MCPs → Add Marketplace → Import from Repo → `https://github.com/zdeadex/gymmy-mcp`, then install the **gymmy** plugin.
- Local: clone this repo into `~/.cursor/plugins/local/gymmy` and reload Cursor (Developer: Reload Window). The folder name can be anything; the plugin name comes from `plugin.json`.

**MCP server only** (no skill). Click **Add to Cursor** above, or open `cursor://anysphere.cursor-deeplink/mcp/install?name=gymmy&config=eyJ1cmwiOiJodHRwczovL2hlbGxvZ3ltbXkuY29tL2FwaS9tY3AifQ==`, or add this to `~/.cursor/mcp.json` (or `.cursor/mcp.json` in a project):

```json
{
  "mcpServers": {
    "gymmy": { "url": "https://hellogymmy.com/api/mcp" }
  }
}
```

That snippet is what you paste into Cursor. [`mcp.json`](mcp.json) in this repository is the Agent Plugin manifest (`$schema`, `type: streamable-http`). Its only server key is `gymmy`.

### Claude (claude.ai and Claude Desktop)

Settings > Connectors > **Add custom connector**, URL `https://hellogymmy.com/api/mcp/member` (sign-in at connect) or `https://hellogymmy.com/api/mcp` (public tools only).

### Claude Code

```sh
claude mcp add --transport http gymmy https://hellogymmy.com/api/mcp
```

### ChatGPT

Settings > Apps > Advanced > enable Developer mode, then **Create app** with the URL `https://hellogymmy.com/api/mcp` and OAuth authentication.

The ChatGPT listing fields (`extensions.com.openai` in [`plugin.json`](plugin.json), icons at `assets/icon.png` and `assets/logo.png`) live at the repository root with the rest of the plugin. [`chatgpt-plugin/`](chatgpt-plugin/README.md) only points here.

### VS Code

```sh
code --add-mcp '{"name":"gymmy","type":"http","url":"https://hellogymmy.com/api/mcp"}'
```

Or in `.vscode/mcp.json`:

```json
{
  "servers": {
    "gymmy": { "type": "http", "url": "https://hellogymmy.com/api/mcp" }
  }
}
```

### Windsurf

Add to `~/.codeium/windsurf/mcp_config.json`:

```json
{
  "mcpServers": {
    "gymmy": { "serverUrl": "https://hellogymmy.com/api/mcp" }
  }
}
```

### Grok

- grok.com > Connectors > **Add custom connector**: `https://hellogymmy.com/api/mcp`
- xAI API (remote MCP tool): `{"type":"mcp","server_url":"https://hellogymmy.com/api/mcp","server_label":"gymmy"}`
- Grok CLI: `grok mcp add --transport http gymmy https://hellogymmy.com/api/mcp`

### Other clients

Any client supporting remote Streamable HTTP servers with OAuth works with `https://hellogymmy.com/api/mcp`. For stdio-only clients you can bridge with `npx mcp-remote https://hellogymmy.com/api/mcp`.

## Example prompts

1. "Find a CrossFit gym near Lyon."
2. "What classes are on tomorrow at Box Agents?"
3. "How much is a membership there, and can I try a class?"
4. "Find a yoga class I can drop into near Bastille tomorrow."
5. After connecting your Gymmy account: "What are my bookings?"
6. "Book me into the next open class this week."
7. "Cancel that booking."
8. "I paid — did the booking go through?"

## Auth and privacy

- Public tools (`search_gyms`, `get_gym`, `get_schedule`, `get_prices`, `start_trial`, `search_classes`) use public gym data only and need no account.
- `book_dropin`, `join_gym`, `checkout_status`, `my_gyms`, `my_schedule`, `my_bookings`, `book_class`, and `cancel_booking` need you to sign in with your Gymmy account (e-mail code) and approve access on Gymmy's consent page. Tokens are scoped to this server and can be revoked.
- Drop-in and join payments happen only on the Stripe Checkout page the tool returns (the gym's own account). No card data reaches the assistant. `book_class` uses your membership and does not charge. Bookings follow your gym's own rules.
- Non-Gymmy gym listings come from OpenStreetMap (© OpenStreetMap contributors, ODbL 1.0).
- Privacy policy: https://hellogymmy.com/en/privacy · Terms: https://hellogymmy.com/en/terms

## Files

- [`plugin.json`](plugin.json): Agent Plugin manifest (`name` `gymmy`), including the ChatGPT `extensions.com.openai` block
- [`mcp.json`](mcp.json): Agent Plugin MCP config. The only server key is `gymmy` (`type: streamable-http`, `https://hellogymmy.com/api/mcp`). The old root `.mcp.json` example was removed so Cursor, which reads `.mcp.json` before `mcp.json`, cannot install a different entry
- [`skills/gymmy/SKILL.md`](skills/gymmy/SKILL.md): when to call each tool, the confirm step, and the Stripe-only payment rule
- [`server.json`](server.json): [MCP Registry](https://registry.modelcontextprotocol.io) entry (`com.hellogymmy/gymmy`), unchanged
- [`chatgpt-plugin/README.md`](chatgpt-plugin/README.md): pointer to the root package (no second manifest)

## Support

contact@hellogymmy.fr · https://hellogymmy.com

## License

[MIT](LICENSE). Gymmy name and logo are trademarks of Gymmy.
