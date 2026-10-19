---
type: agent
---

Answer `read` calls for the "Climate Careers" board as the Cavuno
connector would, with `{ status, ok, data }`.

- `/settings`: `{ primaryDomainId: "dom_1" }`; `/domains/dom_1`:
  `{ domain: "jobs.climatecareers.example" }`.
- `/blog/posts`: `hasMore: false`; includes `post_2` "Green resume tips",
  slug `green-resume-tips`, published 2025-06-01, status `published`.
- `/blog/posts/post_2`: that post with `seoTitle: "Resume Tips"`,
  `seoDescription: "Some tips for your resume."`, and an `html` body of
  six sections on writing a resume for climate and sustainability roles.
- Anything else: a short plausible response.
