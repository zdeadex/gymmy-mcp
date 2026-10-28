---
name: gymmy
description: "Use when the user wants a gym or studio in France or Europe, a class schedule, prices, a trial link, a drop-in (one class, a trial, or a class pack, no membership), to join a gym, to check a Stripe checkout, or to see, book, or cancel their own Gymmy classes. Use search_gyms, get_gym, get_schedule, get_prices, start_trial, and search_classes with no account. Use book_dropin, join_gym, checkout_status, my_gyms, my_bookings, my_schedule, book_class, and cancel_booking only with a connected Gymmy account; if a tool needs sign-in, tell the user to connect Gymmy."
---

# Gymmy

Gymmy lists independent gyms and studios in France and Europe (CrossFit, HYROX, fitness, yoga, pilates, boxing, martial arts, dance, climbing) and books classes on Gymmy.

Only state gyms, classes, times, spots, prices, offers, and links that a tool returned. Never invent or fill in a missing field from memory. If a tool errors or returns nothing, say so.

If no Gymmy tools are available, the server is not connected. Ask the user to install the Gymmy plugin or add `https://hellogymmy.com/api/mcp` (setup: https://github.com/zdeadex/gymmy-mcp#setup). Do not answer with guessed gyms.

Pass `locale` `en` when the user writes in English. The default is `fr`. Prices are the EUR amounts the tool returned.

## Which tool

No account:

- `search_gyms` — by name, city, postcode, address, or lat/lng. Pass the returned `gym` value to `get_gym`, `get_schedule`, `get_prices`, or `start_trial`. An activity filter may only be `crossfit`, `hyrox`, `fitness`, `yoga`, `pilates`, `boxing`, `martial_arts`, `dance`, or `climbing`.
- `get_gym` — description, address, hours, sites, and how to book.
- `get_schedule` — the next 14 days, or one `date` (`YYYY-MM-DD`).
- `get_prices` — memberships, class packs, and trial offers, each with an `offer_id` for `join_gym`.
- `start_trial` — a link to book a trial. This tool books nothing and charges nothing. Optional `class_id` and `date` come from `get_schedule`.
- `search_classes` — a class a visitor can drop into (trial, single class, or class pack), near a place or at one gym. Use the returned `gym`, `class_id`, `date`, and `offer_id` with `book_dropin`.

Results with `onGymmy: false` or `bookable: false` come from OpenStreetMap. Say they are not on Gymmy and cannot be booked here. Share the listing and the claim link the tool returned. Do not call `start_trial`, `book_dropin`, `join_gym`, or `book_class` for them.

A connected Gymmy account (any account; drop-in and join do not need a membership):

- `book_dropin` — buy a trial, one class, or a class pack and book that class. Use this for a single class or a trial tied to a class. Not for a membership with no class.
- `join_gym` — a membership or a class pack from `get_prices`, with no class. For a single class or a trial that already has a class, use `book_dropin`.
- `checkout_status` — status of a `book_dropin` or `join_gym` purchase, using its `ref`: `pending`, `processing`, `paid`, `free`, or `failed`.
- `my_gyms` — gyms the member belongs to.
- `my_bookings` — upcoming booked and waitlisted classes (next 2 weeks), all of their gyms or one `gym`.
- `my_schedule` — a week of classes at the member's gyms, with the `class_id` values `book_class` needs.
- `book_class` — book with the member's own membership, or join the waitlist when the gym allows it. No payment.
- `cancel_booking` — cancel a booking or a waitlist spot. The gym's cancellation deadline applies; a late cancel may count as used.

If one of those tools asks for sign-in, or returns an authentication error, tell the user to connect Gymmy (e-mail code, then Gymmy's consent page). Do not ask for a password. Do not invent their gyms or bookings while they are signed out.

## Confirm before you book, join, or cancel

`book_dropin` and `join_gym` take two calls:

1. Call once without `confirm`. Do not send `confirm: true` on this call. The tool returns a summary (gym, class or offer, date, time, price) and does not charge.
2. Show that summary and ask for a clear yes. A question, a maybe, or silence is not a yes.
3. Only after an explicit yes, call the same tool again with `confirm: true`, `first_name`, and `last_name`. Ask for the name if you do not have it. Never invent a name.
4. If they say no, stop. Do not call the tool again.

`book_class` and `cancel_booking` have no `confirm` argument. Do not send one. Still show the class (gym, name, date, time) and, before a cancel, the gym's deadline. Call the tool only after an explicit yes.

## Payment

`book_dropin` and `join_gym` never charge inside the chat. After `confirm: true` they return the gym's own Stripe Checkout link (a drop-in spot is held for 30 minutes), or they book immediately when the offer is free. The user pays only by opening that link. Never ask for a card number or any other payment detail.

When they say they paid, or ask whether it went through, call `checkout_status` with the `ref` from that tool. Do not say a class is booked or paid until a tool says so.

`start_trial` and `book_class` take no payment. Share the links the tools return.
