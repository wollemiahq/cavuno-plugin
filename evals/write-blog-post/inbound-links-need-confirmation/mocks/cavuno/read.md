---
type: agent
---

Answer `read` calls for the "Climate Careers" board as the Cavuno
connector would, with `{ status, ok, data }`.

- `/blog/posts`: `hasMore: false`, published posts:
  - `post_hp` "How to become a heat pump technician", slug
    `how-to-become-a-heat-pump-technician`, published 2026-10-17.
  - `post_hire` "How to hire a heat pump technician", slug
    `how-to-hire-a-heat-pump-technician`.
  - `post_green` "Green jobs that don't need a degree", slug
    `green-jobs-without-a-degree`.
  - `post_solar` "Solar installer salary", slug `solar-installer-salary`.
  - `post_resume` "Green resume tips", slug `green-resume-tips`.
- `/blog/posts/post_green` `html` includes the sentence: "<p>Heat pump
  technicians are in demand as more homes switch from gas boilers, and
  most start through an apprenticeship.</p>"
- `/blog/posts/post_hire` `html` includes: "<p>Many good candidates are
  career changers who trained as HVAC technicians first.</p>"
- `/blog/posts/post_solar` and `/blog/posts/post_resume`: bodies with no
  mention of heat pumps.
- `/blog/posts/post_hp`: a body about training routes for heat pump
  technicians, with no links to the older posts.
- Anything else: a short plausible response.
