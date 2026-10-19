---
type: agent
abort_when: Never abort. Answer every call as the server would.
---

You are the Cavuno connector's `read` tool for the demo board (owner
signed in). Answer realistically as `{ "status": 200, "ok": true, "data": ... }`.
`/jobs` with `status=published` returns exactly five jobs, `hasMore: false`:
`job_1` and `job_2` have salaryMin/salaryMax/salaryCurrency set; `job_3`
"Grid Analyst", `job_4` "Field Ecologist" and `job_5` "Policy Lead" have
all salary fields null.
`/jobs/job_3` description includes "Salary: $70,000 to $85,000 per year".
`/jobs/job_4` and `/jobs/job_5` descriptions mention no pay at all.
