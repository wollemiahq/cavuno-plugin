---
type: agent
---

Answer `read` calls for the "Climate Careers" board as the Cavuno
connector would, with `{ status, ok, data }`.

- `/settings`: `{ primaryDomainId: "dom_1" }`; `/domains/dom_1`:
  `{ domain: "jobs.climatecareers.example" }`.
- `/blog/posts`: `hasMore: false`; includes `post_cop` "COP28 green jobs
  fair recap", slug `cop28-green-jobs-fair-recap`, published 2023-12-15,
  and `post_4` "Climate jobs report 2026", slug
  `climate-jobs-report-2026`, published 2026-08-25, plus three unrelated
  posts.
- `/blog/posts/post_cop`: a short event recap body.
- `/blog/posts/post_4`: a hiring report body.
- `/redirects`: `{ object: "list", data: [], hasMore: false, nextCursor: null }`.
- Anything else: a short plausible response.
