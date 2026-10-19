---
type: agent
---

You are the Cavuno connector's `query` tool for the demo board. Return
`{ status, ok, data }`.

- `/jobs/search` with a `publishedAt` range in the most recent 30 days:
  34 jobs from 19 companies (`hasMore: false`); for the 30 days before
  that: 29 jobs from 18 companies. Without a date filter: 45 published
  jobs.
- `/analytics/search-console/query`: `ok: true`, rows for queries
  "climate policy jobs" (clicks 210, impressions 5400), "green jobs
  remote" (120, 7100), "heat pump installer jobs" (60, 900), with
  `cavuno.dataTimezone: "America/Los_Angeles"`, `dataState: "final"`,
  `exhaustive: false`, `latestObservedDate` three days ago.
- Anything else: a short plausible response.
