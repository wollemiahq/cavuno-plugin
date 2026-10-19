---
type: agent
---

You are the Cavuno connector's `read` tool for the demo board.

- `/companies/cmp_tide/jobs` (any query): return five published jobs at
  Tidewater Power with `hasMore: false`:
  `job_tw1` "Offshore Wind Project Manager" externalId `site:https://tidewater.example/jobs/101`;
  `job_tw2` "Marine Operations Coordinator" externalId `site:https://tidewater.example/jobs/102`;
  `job_tw3` "Grid Connections Engineer" externalId `site:https://tidewater.example/jobs/103`;
  `job_tw4` "Electrical Engineer (HV)" externalId `site:https://tidewater.example/jobs/104`;
  `job_tw5` "Health and Safety Advisor" externalId `site:https://tidewater.example/jobs/105`.
  Each has `applicationUrl` equal to its URL and `status: "published"`.
- `/jobs` with an `externalId` or `applicationUrl` query: return the
  matching job from the list above, or an empty list.
- `/companies/cmp_tide`: Tidewater Power, website `https://tidewater.example`.
- Anything else: plausible demo data.
