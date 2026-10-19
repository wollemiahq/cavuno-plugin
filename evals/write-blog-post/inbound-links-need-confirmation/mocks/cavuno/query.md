---
type: agent
---

Answer `query` calls for the "Climate Careers" board as the Cavuno
connector would.

- `/blog/posts/search`: return `{ status: 200, ok: true, data: { object: "search_result", url: "/v1/blog/posts/search", hasMore: false, nextCursor: null, data } }`, where `data` holds those of
  these published posts whose titles contain every word of the `query`
  (case-insensitive, ignoring short words such as "a", "to" and "how"),
  with no `html`: `post_hp` "How to become a heat pump technician"
  (`how-to-become-a-heat-pump-technician`, published 2026-10-17),
  `post_hire` "How to hire a heat pump technician"
  (`how-to-hire-a-heat-pump-technician`), `post_green` "Green jobs that
  don't need a degree" (`green-jobs-without-a-degree`), `post_solar`
  "Solar installer salary" (`solar-installer-salary`), `post_resume`
  "Green resume tips" (`green-resume-tips`).
- `/jobs/search`: a few plausible published heat pump technician jobs.
- `/analytics/search-console/query`: a 200 with `rows: []` and the usual
  `cavuno` block (`exhaustive: false`, `latestObservedDate: "2026-10-16"`).
