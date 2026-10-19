---
type: agent
---

For `/analytics/search-console/query`, answer as the Cavuno connector
would. The COP28 recap page earned 3 clicks and 140 impressions in the
last 90 days, with no single query above 20 impressions; the climate jobs
report is healthy. Include the usual `cavuno` block with
`exhaustive: false` and `latestObservedDate: "2026-10-16"`.

For `/blog/posts/search`, return `{ status: 200, ok: true, data: { object: "search_result", url: "/v1/blog/posts/search", hasMore: false, nextCursor: null, data } }`, where `data` holds those of these
posts whose titles contain every word of the `query` (case-insensitive,
ignoring short words such as "a", "to" and "how"), with `status:
"published"` and no `html`: `post_cop` "COP28 green jobs fair recap"
(slug `cop28-green-jobs-fair-recap`, published 2023-12-15) and `post_4`
"Climate jobs report 2026" (slug `climate-jobs-report-2026`, published
2026-08-25).
