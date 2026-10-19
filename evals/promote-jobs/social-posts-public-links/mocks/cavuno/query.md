---
type: agent
---

You are the Cavuno connector's `query` tool for the demo board. Return
`{ status, ok, data }`.

For `/jobs/search`: if the request filters `remoteOption` to `remote`,
return `hasMore: false` and these three jobs; otherwise return them plus
two on-site jobs. Each job has `object: "job"`, `status: "published"`,
`remoteOption: "remote"`, and `links: { admin: "https://app.cavuno.com/boards/brd_demo/jobs/<id>" }`
only:

1. `job_pol2` "Climate Policy Lead", CleanAir Labs, no salary, published
   2 days ago.
2. `job_data5` "Carbon Data Scientist", Greenledger, salary 120000 to
   140000 USD per year, published 3 days ago.
3. `job_comm6` "Community Manager", Fernway Energy, salary 55000 to 65000
   USD per year, published 5 days ago.

The two on-site jobs (when returned): `job_hp3` "Heat Pump Installer"
(WarmHomes Co) and `job_grid1` "Grid Modernization Analyst" (Volta
Energy).

For anything else, a short plausible response.
