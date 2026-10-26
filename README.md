<p align="center"><img src="assets/icon-512.png" alt="Gymmy" width="96" height="96"></p>

# Gymmy MCP server

[![Gymmy MCP server](https://mcprush.com/badge/gymmy-mcp/card.svg)](https://mcprush.com/to/gymmy-mcp?ref=badge)

Official [Model Context Protocol](https://modelcontextprotocol.io) server for [Gymmy](https://hellogymmy.com): find gyms and studios, check schedules and prices, book classes and start a free trial from any AI assistant.

Gymmy is the booking and management platform of independent gyms and studios in France and Europe (CrossFit, HYROX, fitness, yoga, pilates, boxing, martial arts, dance). Ask your assistant to find a gym near you, see its class schedule and prices, and get a trial class link. Connect your Gymmy account to see your bookings, book a class with your membership or cancel one, under your gym's own rules. Gyms not on Gymmy yet come from OpenStreetMap and are clearly marked as not bookable.

This is a **hosted remote server**: nothing to install or run.

| | |
| --- | --- |
| MCP URL | `https://hellogymmy.com/api/mcp` |
| MCP URL, sign-in at connect (Claude) | `https://hellogymmy.com/api/mcp/member` |
| Transport | Streamable HTTP (stateless) |
| Auth | OAuth 2.1 + PKCE (S256), Client ID Metadata Documents or Dynamic Client Registration, RFC 8707 resource indicators. Public tools need no sign-in. |
| Registry name | `com.hellogymmy/gymmy` |

[![Add to Cursor](https://cursor.com/deeplink/mcp-install-dark.svg)](https://cursor.com/en/install-mcp?name=gymmy&config=eyJ1cmwiOiJodHRwczovL2hlbGxvZ3ltbXkuY29tL2FwaS9tY3AifQ==)

## Tools

| Tool | Sign-in | What |
| --- | --- | --- |
| `search_gyms` | no | Find gyms by name, place or coordinates. Gymmy gyms are bookable; OpenStreetMap listings are flagged not on Gymmy |
| `get_gym` | no | Description, address, hours, sites, how to book |
| `get_schedule` | no | Next 14 days of classes, with a link per class |
| `get_prices` | no | Offers with EUR prices and a sign-up link each |
| `start_trial` | no | Trial class link (offer and class picked). Books nothing |
| `my_gyms` | yes | Gyms of the signed-in member |
| `my_schedule` | yes | Week of classes with class ids |
| `my_bookings` | yes | Upcoming booked and waitlisted classes |
| `book_class` | yes | Books with the member's own membership (gym rules apply, no payment) |
| `cancel_booking` | yes | Cancels a booking (the gym's cancel deadline applies) |

Every tool has a `title` and `readOnlyHint` / `destructiveHint` annotations. Calling a member tool without signing in starts the OAuth sign-in.

## Setup

### Cursor

Click **Add to Cursor** above, or open `cursor://anysphere.cursor-deeplink/mcp/install?name=gymmy&config=eyJ1cmwiOiJodHRwczovL2hlbGxvZ3ltbXkuY29tL2FwaS9tY3AifQ==`, or add this to `~/.cursor/mcp.json` (or `.cursor/mcp.json` in a project):

```json
{
  "mcpServers": {
    "gymmy": { "url": "https://hellogymmy.com/api/mcp" }
  }
}
```

### Claude (claude.ai and Claude Desktop)

Settings > Connectors > **Add custom connector**, URL `https://hellogymmy.com/api/mcp/member` (sign-in at connect) or `https://hellogymmy.com/api/mcp` (public tools only).

### Claude Code

```sh
claude mcp add --transport http gymmy https://hellogymmy.com/api/mcp
```

### ChatGPT

Settings > Apps > Advanced > enable Developer mode, then **Create app** with the URL `https://hellogymmy.com/api/mcp` and OAuth authentication.

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
4. After connecting your Gymmy account: "What are my bookings?"
5. "Book me into the next open class this week."
6. "Cancel that booking."

## Auth and privacy

- Public tools (`search_gyms`, `get_gym`, `get_schedule`, `get_prices`, `start_trial`) use public gym data only and need no account.
- Member tools need you to sign in with your Gymmy account (e-mail code) and approve access on Gymmy's consent page. Tokens are scoped to this server and can be revoked.
- Data used for connected members: e-mail, gyms, bookings. No payment data reaches the assistant. Bookings follow your gym's own rules; nothing is charged.
- Non-Gymmy gym listings come from OpenStreetMap (© OpenStreetMap contributors, ODbL 1.0).
- Privacy policy: https://hellogymmy.com/en/privacy · Terms: https://hellogymmy.com/en/terms

## Files

- [`server.json`](server.json): [MCP Registry](https://registry.modelcontextprotocol.io) entry (`com.hellogymmy/gymmy`)
- [`mcp.json`](mcp.json) / [`.mcp.json`](.mcp.json): client configuration example

## Support

contact@hellogymmy.fr · https://hellogymmy.com

## License

[MIT](LICENSE). Gymmy name and logo are trademarks of Gymmy.
