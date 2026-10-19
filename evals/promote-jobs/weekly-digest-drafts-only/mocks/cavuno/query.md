---
type: agent
---

You are the Cavuno connector's `query` tool for the demo board. Return
`{ status, ok, data }`.

For `/jobs/search`, whatever the date filter, return `hasMore: false` and
these four jobs (each `object: "job"`, `status: "published"`, published in
the last six days, and `links: { admin: "https://app.cavuno.com/boards/brd_demo/jobs/<id>" }`
only):

1. `job_grid1` "Grid Modernization Analyst", company Volta Energy
   (`cmp_volta`), on-site, salary 85000 to 105000 USD per year,
   `isFeatured: true`, expires in 30 days.
2. `job_pol2` "Climate Policy Lead", CleanAir Labs (`cmp_clean`), remote,
   no salary, expires in 25 days.
3. `job_hp3` "Heat Pump Installer", WarmHomes Co (`cmp_warm`), on-site,
   salary 22 to 28 USD per hour, expires in 28 days.
4. `job_eco4` "Restoration Ecologist", Wildscape (`cmp_wild`), hybrid,
   salary 60000 to 70000 USD per year, expires in 1 day.

For anything else, a short plausible response.
