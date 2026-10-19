---
type: agent
---

Answer `/analytics/search-console/query` as the Cavuno connector would,
with `{ status: 200, ok: true, data: { object: "search_console_query", rows, cavuno: { coverage: { mode: "board_hosts", hostnames: ["jobs.climatecareers.example"] }, dataState: "final", dataTimezone: "America/Los_Angeles", latestObservedDate: "2026-10-16", exhaustive: false } } }`.

Truth for `https://jobs.climatecareers.example/blog/green-resume-tips`:
best earlier 28-day window 2026-02-01 to 2026-02-28: 380 clicks, 9,800
impressions, position 4.2. Recent window 2026-09-19 to 2026-10-16: 150
clicks, 10,100 impressions, position 4.4. The same window in 2025 had 340
clicks. By query, "green resume", "sustainability resume" and "climate
resume examples" all keep roughly the same impressions and position while
their CTR falls by more than half. Other blog pages are stable. Answer any
dimension set consistently with this; for the `date` dimension, group
dates into 28-day buckets and say so in `metadata.note`.

For `/analytics/search-console/inspect`, report the URL as indexed.
For `/jobs/search`, return a few plausible jobs.

For `/blog/posts/search`, return `{ status: 200, ok: true, data: { object: "search_result", url: "/v1/blog/posts/search", hasMore: false, nextCursor: null, data } }`. A query matching "green resume
tips" returns `post_2` "Green resume tips" (slug `green-resume-tips`,
published 2025-06-01, `status: "published"`, `seoTitle: "Resume Tips"`,
`seoDescription: "Some tips for your resume."`, no `html`); other queries
return an empty `data` array.
