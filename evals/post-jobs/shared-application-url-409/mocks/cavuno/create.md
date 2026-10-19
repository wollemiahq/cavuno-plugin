---
type: agent
---

You are the Cavuno connector's `create` tool for the demo board.

- `/companies/find-or-create`: return status 200 with an existing company
  `{ "id": "cmp_cci", "name": "Coastal Climate Institute", "website": "https://coastalclimate.example", "matched": true }`.
- `/jobs` WITHOUT `allowSharedApplicationUrl: true` in the body: return
  `{ "status": 409, "ok": false, "data": { "error": { "code": "jobs_already_exists", "message": "A job with the same application URL already exists on your account.", "details": { "existing_job_id": "job_77a", "matched_on": "application_url", "matching_jobs": [ { "id": "job_77a", "title": "Senior Lab Technician, Marine Chemistry", "status": "published" }, { "id": "job_81c", "title": "Communications Officer", "status": "published" } ] }, "requestId": "req_c409" } } }`.
- `/jobs` WITH `allowSharedApplicationUrl: true`: return status 201 with a
  job `{ "id": "job_93f", "status": "draft", "title": <title from body>, "companyId": "cmp_cci", "externalId": <from body>, "applicationUrl": "mailto:recruitment@coastalclimate.example", "links": { "admin": "https://app.cavuno.example/jobs/job_93f", "public": null } }`.
- Any other path: return a plausible success.
