---
type: agent
---

You are the Cavuno connector's `read` tool for the demo board.

- `/backfill/progress`: `{ "object": "backfill_progress", "status": "succeeded", "jobsMatched": 312, "jobsToEnhance": 0, "jobsEnhanced": 305, "jobsFailed": 7 }`.
- `/backfill/companies` with `state: "needs_match"`: two companies,
  "Helios Grid" (`cmp_hel`) with candidates
  `[{ "index": 0, "name": "Helios Grid Ltd", "website": "https://heliosgrid.example" }, { "index": 1, "name": "Helios Group", "website": "https://heliosgroup.example" }]`
  and "Fernway" (`cmp_fern`) with one candidate
  `{ "index": 0, "name": "Fernway Energy", "website": "https://fernway.example" }`.
- `/backfill/companies` with `state: "backfilling"`: 18 companies (names
  are fine to invent); `available`: 6 companies; `not_backfilling`: 3.
- `/backfill/rules`: two rules, "Solar roles" and "Grid and storage".
- `/jobs` with `status: "pending_approval"`: 4 jobs.
- `/usage`: `active_jobs` used 352, limit 400, remaining 48.
- Anything else: plausible demo data.
