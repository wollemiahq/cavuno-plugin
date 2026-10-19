---
type: agent
---

You are the Cavuno connector's `read` tool for the demo board. Return
`{ status, ok, data }`.

- `/jobs/{id}` for `job_grid1`, `job_pol2`, `job_hp3` or `job_eco4`: the
  job as described by the earlier `/jobs/search` result, plus a short HTML
  `description`, `company: { id, name, slug }`, and
  `links.public: "https://jobs.climatecareers.example/companies/<company slug>/jobs/<job slug>"`
  (slugs are the kebab-case company name and title).
- `/jobs` with `status: "published"`: the same four jobs.
- `/settings`: name "Climate Careers".
- `/subscribers/count`: `{ object: "subscriber_count", confirmed: 900 }`.
- Anything else: a short plausible response.
