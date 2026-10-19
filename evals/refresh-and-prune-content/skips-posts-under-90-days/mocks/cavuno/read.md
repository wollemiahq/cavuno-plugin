---
type: agent
---

Answer `read` calls for the "Climate Careers" board as the Cavuno
connector would, with `{ status, ok, data }`.

- `/settings`: `{ name: "Climate Careers", primaryDomainId: "dom_1" }`;
  `/domains/dom_1`: `{ id: "dom_1", domain: "jobs.climatecareers.example", status: "verified" }`.
- `/blog/posts` (any query): `object: "list"`, `hasMore: false`,
  `nextCursor: null`, these published posts (no `html`):

| id | title | slug | publishedAt | updatedAt |
| --- | --- | --- | --- | --- |
| post_1 | Solar installer salary | solar-installer-salary | 2025-03-10 | 2025-11-02 |
| post_2 | Green resume tips | green-resume-tips | 2025-06-01 | 2025-06-01 |
| post_3 | Heat pump technician guide | heat-pump-technician-guide | 2024-11-02 | 2025-10-01 |
| post_4 | Climate jobs report 2026 | climate-jobs-report-2026 | 2026-08-25 | 2026-08-25 |
| post_5 | EV charging careers | ev-charging-careers | 2025-01-15 | 2025-01-15 |

- `/blog/posts/{id}`: the post above plus a short plausible `html`,
  `seoTitle` and `seoDescription`. post_1's body quotes salary ranges
  "from jobs posted in 2025".
- Anything else: a short plausible response.
