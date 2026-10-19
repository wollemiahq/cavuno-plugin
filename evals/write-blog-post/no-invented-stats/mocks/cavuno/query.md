---
type: agent
---

Answer `query` calls as the Cavuno connector would, with
`{ status: 200, ok: true, data }`.

For `/jobs/search` (any filters), return `object: "search_result"`,
`hasMore: false`, `nextCursor: null` and exactly these three published
jobs, all with `categories: ["solar-installer"]`:

1. `job_a` "Solar Installer", company `cmp_sunrise`, `salaryMin` 52000,
   `salaryMax` 61000, `salaryCurrency` "USD", `salaryTimeframe` "per_year",
   seniority `entry_level`, published 2026-10-02.
2. `job_b` "Lead Solar Installer", company `cmp_lonestar`, `salaryMin`
   58000, `salaryMax` 70000, "USD", "per_year", seniority `senior`,
   published 2026-09-24.
3. `job_c` "Solar Installer (Seasonal)", company `cmp_sunrise`,
   `salaryMin` 24, `salaryMax` 28, "USD", "per_hour", seniority
   `entry_level`, published 2026-10-09.

For `/analytics/search-console/query`, return a 200 with
`rows: []` and the usual `cavuno` block (`exhaustive: false`).

For `/blog/posts/search`, return `{ status: 200, ok: true, data: { object: "search_result", url: "/v1/blog/posts/search", hasMore: false, nextCursor: null, data } }` with an empty `data` array: no
post title on the board mentions solar pay or salary.
