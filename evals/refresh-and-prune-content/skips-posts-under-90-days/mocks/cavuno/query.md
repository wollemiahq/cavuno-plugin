---
type: agent
---

Answer `query` calls as the Cavuno connector would, with
`{ status: 200, ok: true, data }`.

For `/analytics/search-console/query`, return
`data: { object: "search_console_query", rows, cavuno: { coverage: { mode: "board_hosts", hostnames: ["jobs.climatecareers.example"] }, dataState: "final", dataTimezone: "America/Los_Angeles", latestObservedDate: "2026-10-16", exhaustive: false } }`.
Rows are `{ keys, clicks, impressions, ctr, position }`; pages are full
URLs `https://jobs.climatecareers.example/blog/<slug>`.

The data below is the truth. Keep every answer consistent with it and
return at most 80 rows per call. The recent window is 2026-09-19 to
2026-10-16.

| slug | best earlier 28-day window | clicks | impressions | position | recent clicks | recent impressions | recent position | same window in 2025 |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| solar-installer-salary | 2026-03-14 to 2026-04-10 | 1240 | 21000 | 3.1 | 410 | 15800 | 7.8 | 900 clicks |
| green-resume-tips | 2026-02-01 to 2026-02-28 | 380 | 9800 | 4.2 | 150 | 10100 | 4.4 | 340 clicks |
| heat-pump-technician-guide | 2026-01-04 to 2026-01-31 | 620 | 11000 | 5.0 | 540 | 10400 | 5.2 | 530 clicks |
| climate-jobs-report-2026 | 2026-08-25 to 2026-09-21 | 900 | 14000 | 2.8 | 300 | 7000 | 4.0 | none (new) |
| ev-charging-careers | 2025-12-01 to 2025-12-28 | 12 | 600 | 18.0 | 2 | 300 | 22.0 | 5 clicks |

- A `["page"]` request returns each page's total for the requested range,
  interpolating smoothly between the windows above.
- A request with the `date` dimension returns one row per page per 28-day
  window (`keys[0]` is the window's first date) instead of daily rows, and
  adds `metadata: { note: "Test server: dates grouped into 28-day buckets." }`.
- A `["query"]` request filtered to solar-installer-salary shows
  "solar installer salary" falling from position 2.8 to 8.1. Filtered to
  green-resume-tips, the main queries hold position while CTR falls.

For `/analytics/search-console/inspect`, report the URL as indexed.
For `/jobs/search`, return a few plausible current solar installer jobs
with salary ranges.

For `/blog/posts/search`, return `{ status: 200, ok: true, data: { object: "search_result", url: "/v1/blog/posts/search", hasMore: false, nextCursor: null, data } }`, where `data` holds those of these
posts whose titles contain every word of the `query` (case-insensitive,
ignoring short words such as "a", "to" and "how"), with `status:
"published"` and no `html`: post_1 "Solar installer salary"
(`solar-installer-salary`, published 2025-03-10), post_2 "Green resume
tips" (`green-resume-tips`, 2025-06-01), post_3 "Heat pump technician
guide" (`heat-pump-technician-guide`, 2024-11-02), post_4 "Climate jobs
report 2026" (`climate-jobs-report-2026`, 2026-08-25), post_5 "EV
charging careers" (`ev-charging-careers`, 2025-01-15).
