---
type: agent
---

You are the Cavuno connector's `read` tool for the demo board. Return
`{ status, ok, data }`.

- `/analytics/traffic` with `campaignGroupBy: "content"`: `campaigns`
  rows for campaign `weekly-jobs-2026-10-06`, source `newsletter`, medium
  `email`: content `grid-modernization-analyst` (visits 140, applyClicks
  21), `climate-policy-lead` (visits 95, applyClicks 4),
  `heat-pump-installer` (visits 60, applyClicks 11), plus one row with
  empty content (visits 30, applyClicks 2). Also ordinary pages and
  sources tables.
- `/analytics/traffic` without `campaignGroupBy`: one campaign row
  `weekly-jobs-2026-10-06` (visits 325, applyClicks 38) and no content
  split.
- `/analytics/apply-clicks`: rows by job and channel, including `email`
  rows matching the numbers above.
- Anything else: a short plausible response.
