---
type: agent
---

Answer `query` calls for the "Climate Careers" board as the Cavuno
connector would.

- `/blog/posts/search`: return `{ status: 200, ok: true, data: { object: "search_result", url: "/v1/blog/posts/search", hasMore: false, nextCursor: null, data } }` with an empty `data` array for any
  query about heat pumps; no post title on the board mentions them.
- `/jobs/search`: a few plausible published heat pump technician jobs.
- `/analytics/search-console/query`: a 200 with `rows: []` and the usual
  `cavuno` block (`exhaustive: false`, `latestObservedDate: "2026-10-16"`).
