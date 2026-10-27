---
name: gymmy
description: Use when the user wants to find a gym or fitness studio, see class schedules or prices, get a trial class, or view, book, or cancel their own Gymmy classes.
---

Use the Gymmy MCP tools.

If no Gymmy tools are available, the Gymmy MCP server is not connected: ask the user to add the hosted server `https://hellogymmy.com/api/mcp` (setup per client: https://github.com/zdeadex/gymmy-mcp#setup). Do not guess gym data without it.

- Finding gyms: `search_gyms` with a gym name or a place (or lat/lng). Results with `onGymmy:false` / `bookable:false` come from OpenStreetMap: say they are not on Gymmy and cannot be booked here.
- Gym details: `get_gym`. Classes for the next 14 days: `get_schedule`. Offers and EUR prices: `get_prices`. Trial class link: `start_trial` (books nothing).
- The user's own data needs a connected Gymmy account: `my_gyms`, `my_schedule` (week of classes with class ids), `my_bookings`. If a tool asks for sign-in, tell the user to connect Gymmy.
- `book_class`: first show the class (gym, name, date, time) and ask the user to confirm. Book only after an explicit yes. It uses the member's membership; no payment.
- `cancel_booking`: confirm which booking first and mention the gym's cancel deadline.
- Share the links tools return. Never invent classes, prices, or availability.
