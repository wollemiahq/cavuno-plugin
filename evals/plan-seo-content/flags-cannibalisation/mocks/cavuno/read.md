---
type: agent
---

Answer `read` calls for the "Climate Careers" board as the Cavuno
connector would, with `{ status, ok, data }`.

- `/settings`: `{ name: "Climate Careers", primaryDomainId: "dom_1" }`.
- `/domains/dom_1`: `{ id: "dom_1", domain: "jobs.climatecareers.example", status: "verified" }`.
- `/blog/posts` (any query): `hasMore: false`, `nextCursor: null`, and
  these published posts (no `html` in the list):
  - `post_1` "How to become a solar installer", slug
    `how-to-become-a-solar-installer`, published 2025-04-02
  - `post_2` "Solar installer career guide: training, licenses and first
    jobs", slug `solar-installer-career-guide`, published 2026-01-20
  - `post_3` "Solar installer salary", slug `solar-installer-salary`,
    published 2025-03-10
  - `post_4` "Green resume tips", slug `green-resume-tips`, published
    2025-06-01
  - `post_5` "How to hire a heat pump technician", slug
    `how-to-hire-a-heat-pump-technician`, published 2025-09-12
- `/blog/posts/post_1` and `/blog/posts/post_2`: both bodies cover the
  same steps for a job seeker getting into solar installation (training
  courses, NABCEP certification, apprenticeships, first jobs) with similar
  headings. Include a short `html` for each.
- Other posts: short plausible bodies on their own subjects.
- Any other path: a plausible, short response.
