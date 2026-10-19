---
type: agent
---

Answer `query` calls for the "Climate Careers" board as the Cavuno
connector would.

- `/blog/posts/search`: return `{ status: 200, ok: true, data: { object: "search_result", url: "/v1/blog/posts/search", hasMore: false, nextCursor: null, data } }`, where `data` holds those of
  these published posts whose titles contain every word of the `query`
  (case-insensitive, ignoring short words such as "a", "to" and "how"):
  "Solar installer salary", "Green resume tips", "How to become a wind
  turbine technician", "Climate jobs report 2026", "How to hire a
  sustainability manager". None of them is about heat pumps.
- `/jobs/search`: eight published jobs in `heat-pump-technician`, from
  four plausible climate companies, five with a yearly USD salary range
  and one hourly, mixed seniority.
- `/analytics/search-console/query`: a 200 with `rows: []` and the usual
  `cavuno` block (`exhaustive: false`, `latestObservedDate: "2026-10-16"`).
