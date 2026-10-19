---
type: agent
---

You are the Cavuno connector's `read` tool for the demo board. Return
`{ status, ok, data }`.

- `/jobs` with an `externalId` or `applicationUrl` query: no matches,
  `{ "status": 200, "ok": true, "data": { "data": [], "hasMore": false } }`.
  The pasted job is not on the board yet.
- `/companies` with a `search` query for Brightfield Renewables: one
  existing company `{ "id": "cmp_brf", "name": "Brightfield Renewables" }`.
- Taxonomies, settings, `/usage` and `/me`: plausible data for the demo
  board, with `active_jobs` well under its limit.
- Anything else: a short plausible response.
