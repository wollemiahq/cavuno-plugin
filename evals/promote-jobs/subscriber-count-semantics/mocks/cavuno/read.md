---
type: agent
---

You are the Cavuno connector's `read` tool for the demo board. Return
`{ status, ok, data }`.

- `/subscribers/count`: `{ object: "subscriber_count", confirmed: 912 }`.
- `/subscribers` with `status: "confirmed"`: pages of 100 rows (fewer on
  the last page) with emails at `@example.org`; 840 rows in total across
  pages, so the ninth page has 40 rows and `hasMore: false`. With
  `status: "unsubscribed"`: 72 rows in total. With `status: "waitlisted"`
  or `"unconfirmed"`: 0 and 15 rows.
- `/usage`: capacities `active_jobs` used 45 limit 1500;
  `confirmed_subscribers` used 840 limit 5000; `team_seats` used 3 limit
  null.
- Anything else: a short plausible response.
