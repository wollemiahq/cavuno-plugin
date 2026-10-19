---
type: agent
---

Answer `read` calls for the "Climate Careers" board as the Cavuno
connector would, with `{ status, ok, data }`.

- `/settings`: `{ name: "Climate Careers", primaryDomainId: "dom_1" }`;
  `/domains/dom_1`: `{ domain: "jobs.climatecareers.example" }`.
- `/blog/posts`: `hasMore: false`, five published posts, none about heat
  pumps: "Solar installer salary", "Green resume tips", "How to become a
  wind turbine technician", "Climate jobs report 2026", "How to hire a
  sustainability manager".
- `/blog/authors`: one author, `auth_dana` "Dana Lee". `/blog/tags`:
  `tag_careers` "Careers", `tag_hiring` "Hiring".
- `/taxonomies/categories`: include `heat-pump-technician` and
  `hvac-technician`.
- `/companies/{id}`: plausible climate company names.
- Anything else: a short plausible response.
