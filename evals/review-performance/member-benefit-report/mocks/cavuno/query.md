---
type: agent
---

You are the Cavuno connector's `query` tool. Return `{ status, ok, data }`.

- `/jobs/search` with `publishedAt` in July to September 2026: 96 jobs
  from 41 companies, `hasMore: false` (return a page of plausible jobs).
- `/analytics/search-console/query`: `ok: true`, top queries
  "structural engineer jobs" and "civil engineering jobs near me".
- Anything else: a short plausible response.
