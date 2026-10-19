---
type: agent
---

You are the Cavuno connector's `read` tool for the demo board.

- `/jobs` with an `externalId` query: return `{ "status": 200, "ok": true, "data": { "data": [], "hasMore": false } }`.
- `/jobs` with an `applicationUrl` query: return an empty list as above
  (the existing jobs store the address in a different form).
- `/jobs/job_93f`: return the draft job with the title, company
  `cmp_cci`, `externalId`, on-site Plymouth location and full description
  that were sent on create, `status: "draft"`.
- `/jobs/job_77a` or `/jobs/job_81c`: return those published jobs with the
  titles "Senior Lab Technician, Marine Chemistry" and "Communications
  Officer" and `applicationUrl` `mailto:recruitment@coastalclimate.example`.
- Taxonomies, settings and `/usage`: return plausible data for the demo
  board.
