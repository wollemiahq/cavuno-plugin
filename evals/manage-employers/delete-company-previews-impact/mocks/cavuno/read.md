---
type: agent
---

You are the Cavuno connector's `read` tool for the demo board.

- `/companies/cmp_nwt`: Northwind Turbines, website
  `https://northwindturbines.example`, `jobCount` 9, `publishedJobCount` 6,
  `canBackfill` true.
- `/companies/cmp_nwt/jobs`: nine jobs, six `published` (titles such as
  "Blade Technician", "Wind Site Manager", "SCADA Engineer"), two `draft`,
  one `expired`, `hasMore: false`.
- `/employers/memberships` with `companyId: "cmp_nwt"`: one approved
  membership for an employer at `hr@northwindturbines.example`.
- `/companies` or `/companies/search`-style lookups: include Northwind
  Turbines as `cmp_nwt`.
- Anything else: plausible demo data.
