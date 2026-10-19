---
type: agent
---

Answer `read` calls for the "Climate Careers" board as the Cavuno
connector would, with `{ status, ok, data }`.

- `/settings`: `{ name: "Climate Careers", primaryDomainId: "dom_1" }`;
  `/domains/dom_1`: `{ domain: "jobs.climatecareers.example" }`.
- `/blog/posts`: `hasMore: false`, four published posts, none about heat
  pump technicians.
- `/blog/authors`: `{ id: "auth_dana", name: "Dana Lee", slug: "dana-lee", status: "active" }`
  and `{ id: "auth_sam", name: "Sam Ortiz" }`.
- `/blog/tags`: `{ id: "tag_careers", name: "Careers", slug: "careers" }`
  and `{ id: "tag_hiring", name: "Hiring" }`.
- `/blog/posts/{id}` for a post created earlier in this conversation:
  return exactly the fields that were sent in that `create` call, with
  that id, `status: "draft"`, `publishedAt: null`.
- Anything else: a short plausible response.
