# Claude connectors directory: Gymmy submission

Copy-paste answers for Anthropic's connectors directory (claude.ai, Claude Desktop, Claude mobile), in the order of the submission portal. Docs: [submission](https://claude.com/docs/connectors/building/submission), [review criteria](https://claude.com/docs/connectors/building/review-criteria).

**Where:** https://claude.ai/directory/manage → **Submit new** → **MCP connector** (not *Plugin bundle*).
**Who:** a Pro, Max, Ultra, Team or Enterprise account (Free cannot submit); on Team/Enterprise, an Owner. The listing belongs to the organization you submit from, so submit from the one that should own Gymmy's listing.
Progress is saved in the browser only.

## Before you open the portal

- [ ] Add `https://hellogymmy.com/api/mcp` as a custom connector in Claude (Settings → Connectors) and run **every** tool once, signed out and signed in (the portal asks you to confirm this).
- [ ] Errors are explicit (no bare "Internal Server Error" / "Bad Request").
- [ ] Privacy policy at https://hellogymmy.com/en/privacy covers data collected, use and storage, third-party sharing (Stripe, OpenStreetMap), retention, and a contact. A missing or incomplete policy is an immediate rejection.
- [ ] Icon ready: [`assets/icon-512.png`](../assets/icon-512.png) (or [`assets/mark.svg`](../assets/mark.svg)).

## 1. Connection

Server URL: `https://hellogymmy.com/api/mcp`
Single URL (not per-customer).

## 2. Tools

Synced from the server. All 15 tools have a `title` and `readOnlyHint` / `destructiveHint`; names are under 64 characters. Fix any tool the portal flags on the server, then resync.

## 3. Listing

| Field | Value |
| --- | --- |
| Server name | Gymmy |
| One-liner (≤ 200) | Find gyms and studios in France and Europe, see classes and prices, and book with your Gymmy account. |
| Categories (1–5) | the closest of: Lifestyle, Health & fitness, Booking / scheduling |
| Documentation URL | https://github.com/zdeadex/gymmy-mcp |
| Privacy policy URL | https://hellogymmy.com/en/privacy |
| Support contact | contact@hellogymmy.fr |
| Icon | `assets/icon-512.png` |
| URL slug (permanent) | `gymmy` |

Description (≤ 2,000):

> Gymmy is the booking and management platform of independent gyms and studios in France and Europe (CrossFit, HYROX, fitness, yoga, pilates, boxing, martial arts, dance, climbing, padel). Ask Claude to find a gym near you, see its class schedule and prices, and get a trial class link, with no account. Connect your Gymmy account (e-mail code) to see your gyms and upcoming bookings, book a class with your membership or cancel one under your gym's own rules, or buy a drop-in (trial, single class, class pack) or a membership. Purchases are never charged in the chat: after you confirm, Gymmy returns the gym's own Stripe Checkout page, and no card data reaches Claude. Gyms not on Gymmy yet come from OpenStreetMap and are clearly marked as not bookable.

## 4. Use cases

- Primary use cases: find a gym or studio nearby; check a class schedule and prices; get a trial class link; see my bookings; book or cancel a class with my membership; buy a drop-in or membership via the gym's Stripe Checkout.
- Setup before connecting: none for public tools. Member tools need a free Gymmy account (e-mail code); booking with a membership needs a membership at a Gymmy gym.
- Reads or writes: **both** (reads gyms, schedules, prices, bookings; writes bookings and cancellations; creates checkout sessions).

## 5. Company

Gymmy · https://hellogymmy.com · primary contact for review updates: an inbox you watch (e.g. contact@hellogymmy.fr).

## 6. Authentication

- Requirement: **required when the server asks**: public tools work signed out; member tools trigger sign-in.
- Method: OAuth 2.1 + PKCE (S256), Dynamic Client Registration / Client ID Metadata Documents, RFC 8707 resource indicators. No Anthropic-held client ID/secret needed.

## 7. Data handling

- API: **our own first-party API** (hellogymmy.com). Non-Gymmy gym listings come from OpenStreetMap (ODbL), shown as such.
- Personal health data: no. Sponsored content: no.

## 8. Test & launch

Confirm every tool was run (see checklist above). **Do not enter credentials in the portal.** If the listing is escalated to Verified review, a reviewer emails for test access: then send a member account at **Gymmy Demo** with an active membership, classes in the coming days, and a free or Stripe test-mode drop-in offer. Sign-in is by e-mail code, so give an inbox the reviewer can read or a fixed code for that account.

## 9. Compliance

Seven acknowledgments, all required. Read each one. Note on **financial transactions**: the directory does not support money transfers. Gymmy only returns the gym's Stripe Checkout link after an explicit confirmation and never moves money itself. Answer this one accurately; if it is unclear, ask `mcp-review@anthropic.com` before submitting.

## 10. Review and submit

Every submission is scanned and listed as a **Community connector** by default; some are escalated to **Verified** review (slower, a reviewer tests each tool). No fixed timeline. Status: https://claude.ai/directory/manage. Questions: `mcp-review@anthropic.com`.

## Reference: tools

| Tool | Sign-in | Read-only | What |
| --- | --- | --- | --- |
| `search_gyms` | no | yes | Find gyms by name, place or coordinates |
| `get_gym` | no | yes | Description, address, hours, sites, how to book |
| `get_schedule` | no | yes | Next 14 days of classes |
| `get_prices` | no | yes | Offers with EUR prices and a sign-up link each |
| `start_trial` | no | yes | Trial class link. Books and charges nothing |
| `search_classes` | no | yes | Classes open to drop-in visitors |
| `book_dropin` | yes | no | Summary first; with `confirm: true`, the gym's Stripe Checkout link (or a booking when free) |
| `join_gym` | yes | no | Same confirm-then-Stripe pattern for a membership or class pack |
| `checkout_status` | yes | yes | Status of a `book_dropin` / `join_gym` checkout |
| `get_my_account` | yes | yes | Who is signed in: name, e-mail, membership status, linked gyms |
| `my_gyms` | yes | yes | Gyms of the signed-in member |
| `my_bookings` | yes | yes | Upcoming booked and waitlisted classes |
| `my_schedule` | yes | yes | Week of classes with class ids |
| `book_class` | yes | no | Books with the member's own membership, no payment |
| `cancel_booking` | yes | no (destructive) | Cancels a booking; the gym's deadline applies |

## Reference: example prompts

1. "Find a CrossFit gym near Lyon."
2. "What classes are on tomorrow at that gym?"
3. "How much is a membership there, and can I try a class?"
4. "Find a yoga class I can drop into near Bastille tomorrow."
5. After connecting: "What are my bookings?"
6. "Book me into the next open class this week." then "Cancel that booking."
