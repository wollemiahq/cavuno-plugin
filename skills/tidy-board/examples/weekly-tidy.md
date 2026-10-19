# Example: weekly tidy

## Input

"Tidy up the board."

## Reads

- `/usage`: `active_jobs` 482 of 500 (18 left).
- `/settings`: `defaultJobDurationDays` 30, approval on for free jobs.
- `/jobs?status=pending_approval`: 2 jobs; details read for both.
- `/jobs/search` published with `publishedAt.lte` 30 days ago: 9 jobs.
- Duplicate scan: 1 pair with the same `applicationUrl` and title.
- Completeness sample (20 job details): 4 remote jobs without
  `remotePermits`, 6 jobs without salary (2 state pay in the description).

## What Claude shows

| Group | Count | Sample | Proposed action |
| --- | --- | --- | --- |
| Pending | 2 | "Solar Installer" at SunCo (job_p1): fine. "Earn $$$ from home" (job_p2): no employer, asks applicants to pay | Approve job_p1; reject job_p2 (permanent, separate yes) |
| Stale (over 30 days) | 9 | "Wind Technician" at Gale (job_s1), "Policy Analyst" at GreenGov (job_s2), ... | Expire 8; hold the paid post (job_s4) and ask its employer first |
| Duplicates | 1 pair | "Data Analyst" at Terra: job_d1 (published, has salary) and job_d2 (published, no salary) | Expire job_d2 |
| Incomplete | 4 + 2 | 4 remote jobs missing eligible countries; 2 jobs whose description states pay | Fill the 2 salaries from the text; ask employers for the 4 locations |

"Which groups should I run? Approving job_p1 uses 1 of your 18 slots."

## After the user says "all except the paid stale job, and reject job_p2"

1. `create` `/jobs/batch`: publish job_p1, expire 8 stale jobs, expire
   job_d2, `PATCH` 2 salaries (12 operations). All 12 succeeded.
2. Separate yes confirmed: `delete` `/jobs/job_p2` → 204.
3. `read` `/jobs/job_s1`, `/jobs/job_d2`, `/jobs/job_p1`: states match.

## Report

Approved 1, rejected 1, expired 9 (8 stale, 1 duplicate), added salary to 2.
Active jobs now 474 of 500. Left for you: ask the employer behind job_s4 (paid), and
ask 4 employers which countries their remote roles accept.
