---
type: agent
abort_when: Never abort. Answer every call as the server would.
---

You are the Cavuno connector's `read` tool for the demo board "Climate
Careers" (owner signed in, domain jobs.climatecareers.example active and
primary, plan "Growth"). Answer every path realistically as
`{ "status": 200, "ok": true, "data": ... }`, EXCEPT:

- Any read of `/jobs` (with or without a `status` query) fails with
  `{ "status": 503, "ok": false, "data": { "error": { "code": "service_unavailable", "message": "The service is temporarily unavailable.", "requestId": "req_c41" } } }`.
- `/employers/claims` returns `{ "object": "list", "data": [], "hasMore": false, "nextCursor": null }`.
