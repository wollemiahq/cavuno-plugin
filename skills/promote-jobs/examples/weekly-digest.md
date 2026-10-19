# Example: weekly jobs digest

## Input

"Put together this week's jobs newsletter." (Sent on Monday 2026-10-19.)

## Plan

1. `query` `/jobs/search`:
   `{ "filters": { "status": ["published"], "publishedAt": { "gte": "2026-10-12T00:00:00Z", "lte": "2026-10-19T23:59:59Z" } }, "limit": 100 }`
   → 6 jobs, `hasMore: false`.
2. Drop one job expiring tomorrow. Order: the featured job, then newest.
3. `read` `/jobs/{id}` for the 5 remaining jobs to get `links.public`,
   company and salary.
4. Tag links: `utm_source=newsletter&utm_medium=email&utm_campaign=weekly-jobs-2026-10-19&utm_content=<job slug>`.

## Output

**Subject:** 5 new climate jobs this week
**Preview:** Grid analyst at Volta Energy, remote policy lead at CleanAir

This week brought five new roles, two of them fully remote, and the first
posting from Volta Energy.

**Grid modernization analyst** at Volta Energy · Denver, CO · $85,000–$105,000 a year
Model storage projects for a regional utility program.
https://jobs.climatecareers.example/companies/volta-energy/jobs/grid-modernization-analyst?utm_source=newsletter&utm_medium=email&utm_campaign=weekly-jobs-2026-10-19&utm_content=grid-modernization-analyst

(four more blocks in the same shape)

See all jobs: https://jobs.climatecareers.example/jobs?utm_source=newsletter&utm_medium=email&utm_campaign=weekly-jobs-2026-10-19

---

"This is a draft; nothing was sent. Paste it into your newsletter tool.
Next Monday you can compare visits and apply clicks for campaign
weekly-jobs-2026-10-19."
