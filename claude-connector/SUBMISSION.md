# Claude connectors directory: Gymmy submission

Copy for Anthropic's connectors directory (claude.ai, Claude Desktop, Claude mobile). Submit it through Anthropic's MCP directory submission form. This file only holds the answers; the form and review happen on Anthropic's side.

## Listing

| Field | Value |
| --- | --- |
| Name | Gymmy |
| Server URL | `https://hellogymmy.com/api/mcp` |
| Server URL, sign-in at connect | `https://hellogymmy.com/api/mcp/member` |
| Transport | Streamable HTTP (stateless) |
| Auth | OAuth 2.1 + PKCE (S256), Client ID Metadata Documents or Dynamic Client Registration, RFC 8707 resource indicators. Public tools work without sign-in |
| Category | Lifestyle |
| Tagline | Find and book gym classes |
| Website | https://hellogymmy.com |
| Support | contact@hellogymmy.fr |
| Privacy policy | https://hellogymmy.com/en/privacy |
| Terms | https://hellogymmy.com/en/terms |
| Logo / icon | [`assets/icon-512.png`](../assets/icon-512.png), [`assets/mark.svg`](../assets/mark.svg), [`assets/logo.png`](../assets/logo.png) |
| Source / docs | https://github.com/zdeadex/gymmy-mcp |

### Description

Gymmy is the booking and management platform of independent gyms and studios in France and Europe (CrossFit, HYROX, fitness, yoga, pilates, boxing, martial arts, dance). Ask Claude to find a gym near you, see its class schedule and prices, and get a trial class link. Connect your Gymmy account to see your bookings, book a class with your membership or cancel one, under your gym's own rules, or buy a drop-in or membership through the gym's own Stripe Checkout page. Gyms not on Gymmy yet come from OpenStreetMap and are clearly marked as not bookable.

### Description (fr-FR)

Gymmy est la plateforme de réservation et de gestion des salles et studios indépendants en France et en Europe (CrossFit, HYROX, fitness, yoga, pilates, boxe, arts martiaux, danse). Demande à Claude de trouver une salle près de chez toi, de voir son planning et ses tarifs, et d’obtenir un lien de séance d’essai. Connecte ton compte Gymmy pour voir tes réservations, réserver un cours avec ton abonnement ou l’annuler selon les règles de ta salle, ou acheter une séance ou un abonnement sur la page Stripe Checkout de la salle. Les salles pas encore sur Gymmy viennent d’OpenStreetMap et sont signalées comme non réservables.

## Tools

Every tool has a `title` and `readOnlyHint` / `destructiveHint` annotations.

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
| `my_gyms` | yes | yes | Gyms of the signed-in member |
| `my_bookings` | yes | yes | Upcoming booked and waitlisted classes |
| `my_schedule` | yes | yes | Week of classes with class ids |
| `book_class` | yes | no | Books with the member's own membership, no payment |
| `cancel_booking` | yes | no (destructive) | Cancels a booking; the gym's deadline applies |

Payment never happens in the chat: only on the Stripe Checkout page returned after an explicit confirmation. No card data reaches Claude.

## Example prompts

1. "Find a CrossFit gym near Lyon."
2. "What classes are on tomorrow at Box Agents?"
3. "How much is a membership there, and can I try a class?"
4. "Find a yoga class I can drop into near Bastille tomorrow."
5. After connecting: "What are my bookings?"
6. "Book me into the next open class this week." then "Cancel that booking."

## Reviewer notes

- Test account: a Gymmy member account at the gym **Gymmy Demo**, with an active membership and classes in the next 7 days. Sign-in is by e-mail code, so give the reviewers an inbox they can read, or a fixed code for that account. Fill in the credentials only in the submission form, never in this repository.
- Expected flows (also in `extensions.com.openai.review` in [`plugin.json`](../plugin.json)):
  - `search_gyms` lists Gymmy gyms as bookable and OpenStreetMap listings as not on Gymmy.
  - `get_schedule` / `get_prices` / `start_trial` work without sign-in and book or charge nothing.
  - `my_bookings` after OAuth lists the reviewer's bookings at Gymmy Demo.
  - `book_class` then `cancel_booking`: Claude asks before each call; no payment.
  - `book_dropin` first call returns a summary only; after an explicit yes and `confirm: true`, a Stripe Checkout link (use a free or Stripe test-mode offer at Gymmy Demo).
- OAuth: the consent page is Gymmy's own; tokens are scoped to this server (RFC 8707) and can be revoked by the member.

## Checklist before submitting

- [ ] `https://hellogymmy.com/api/mcp` reachable from Anthropic's IP ranges (no geo or bot blocking)
- [ ] Claude's OAuth callback allowed: `https://claude.ai/api/mcp/auth_callback`
- [ ] Reviewer account ready at Gymmy Demo, with classes scheduled during the review window
- [ ] Tested end to end in claude.ai as a custom connector, on web and mobile
