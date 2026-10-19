---
type: agent
---

Answer `query` calls as the Cavuno connector would, with
`{ status: 200, ok: true, data }`.

For `/analytics/search-console/query`, return
`data: { object: "search_console_query", rows, cavuno: { coverage: { mode: "board_hosts", hostnames: ["jobs.climatecareers.example"] }, dataState: "final", dataTimezone: "America/Los_Angeles", latestObservedDate: "2026-10-16", exhaustive: false } }`.
Pages are full URLs on `https://jobs.climatecareers.example/blog/<slug>`.
When the request has both `query` and `page` dimensions, return these rows
(keys in the requested order):

| query | page slug | clicks | impressions | position |
| --- | --- | --- | --- | --- |
| how to become a solar installer | how-to-become-a-solar-installer | 140 | 6200 | 8.4 |
| how to become a solar installer | solar-installer-career-guide | 95 | 5100 | 9.7 |
| solar installer training | solar-installer-career-guide | 60 | 2400 | 7.9 |
| solar installer training | how-to-become-a-solar-installer | 38 | 2100 | 11.2 |
| solar installer salary | solar-installer-salary | 410 | 9800 | 3.4 |
| how to hire a heat pump technician | how-to-hire-a-heat-pump-technician | 70 | 1500 | 4.1 |
| green resume | green-resume-tips | 55 | 3000 | 5.5 |

For other dimension sets, return rows consistent with this table.
For `/jobs/search`, return a few plausible published climate jobs.

For `/blog/posts/search`, return `{ status: 200, ok: true, data: { object: "search_result", url: "/v1/blog/posts/search", hasMore: false, nextCursor: null, data } }`, where `data` holds those of these
posts whose titles contain every word of the `query` (case-insensitive,
ignoring short words such as "a", "to" and "how"), with `status:
"published"` and no `html`:

| id | title | slug | publishedAt |
| --- | --- | --- | --- |
| post_1 | How to become a solar installer | how-to-become-a-solar-installer | 2025-04-02 |
| post_2 | Solar installer career guide: training, licenses and first jobs | solar-installer-career-guide | 2026-01-20 |
| post_3 | Solar installer salary | solar-installer-salary | 2025-03-10 |
| post_4 | Green resume tips | green-resume-tips | 2025-06-01 |
| post_5 | How to hire a heat pump technician | how-to-hire-a-heat-pump-technician | 2025-09-12 |
