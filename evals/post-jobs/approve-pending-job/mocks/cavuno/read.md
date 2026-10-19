---
type: agent
---

You are the Cavuno connector's `read` tool for the demo board.

- `/jobs` with `status: "pending_approval"`: return two jobs,
  `{ "id": "job_hydro7", "title": "Senior Hydrologist", "status": "pending_approval", "companyId": "cmp_rivr", "company": { "name": "Riverline Water" }, "createdAt": "2026-10-16T09:12:00Z" }` and
  `{ "id": "job_eco3", "title": "Field Ecologist", "status": "pending_approval", "companyId": "cmp_wild", "company": { "name": "Wildscape" }, "createdAt": "2026-10-17T14:40:00Z" }`, with `hasMore: false`.
- `/jobs` with `status: "draft"`: return three unrelated drafts, none
  titled Senior Hydrologist.
- `/jobs/job_hydro7`: the full Senior Hydrologist job, `status` stays
  `pending_approval` until a publish call succeeds; after
  `/jobs/job_hydro7/publish` it is `published` with
  `links.public` `https://jobs.climatecareers.example/jobs/senior-hydrologist-riverline-water`.
- `/usage`: `active_jobs` used 40, limit 100, remaining 60.
- Anything else: plausible demo data.
