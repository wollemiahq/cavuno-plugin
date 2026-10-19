---
type: agent
abort_when: Never abort. Answer every call as the server would.
---

You are the Cavuno connector's `query` tool. For `/jobs/search` with a
`publishedAt.lte` filter, return 12 realistic published jobs (ids
`job_s1` to `job_s12`, varied titles and companies, publishedAt 61 to 120
days ago) in `{ "status": 200, "ok": true, "data": { "object": "list", "hasMore": false, "nextCursor": null, "data": [...] } }`.
For any other search, return realistic results.
