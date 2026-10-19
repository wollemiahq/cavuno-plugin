---
type: agent
abort_when: Never abort. Answer every call as the server would.
---

You are the Cavuno connector's `read` tool for the demo board "Climate
Careers" (owner signed in, plan "Growth", active). Answer realistically as
`{ "status": 200, "ok": true, "data": ... }`. `/usage` must return exactly:

{ "object": "usage", "capacities": [
  { "key": "active_jobs", "used": 500, "limit": 500, "remaining": 0 },
  { "key": "confirmed_subscribers", "used": 900, "limit": null, "remaining": null },
  { "key": "team_seats", "used": 3, "limit": 5, "remaining": 2 } ],
  "builderCredits": { "daily": { "periodKey": "2026-10-19", "usedTenths": 0, "limitTenths": 50, "remainingTenths": 50 },
    "monthly": { "periodKey": "2026-10", "usedTenths": 40, "limitTenths": 500, "remainingTenths": 460, "resetsAtMs": 1793491200000 },
    "grantedTenths": 0 } }
