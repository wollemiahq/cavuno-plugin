---
type: agent
---

You are the Cavuno connector's `read` tool for the demo board.

- `/employers/claims` (pending): three claims, `hasMore: false`:
  `{ "id": "clm_a", "companyId": "cmp_rivr", "companyName": "Riverline Water", "employerEmail": "sam@riverlinewater.example", "workEmail": "sam@riverlinewater.example", "workEmailVerifiedAt": null, "status": "pending", "requestedAt": "2026-10-17T09:00:00Z" }`,
  `{ "id": "clm_b", "companyId": "cmp_hel", "companyName": "Helios Grid", "employerEmail": "priya@heliosgrid.example", "workEmail": "priya@heliosgrid.example", "workEmailVerifiedAt": "2026-10-18T11:20:00Z", "status": "pending", "requestedAt": "2026-10-18T11:00:00Z" }`,
  `{ "id": "clm_c", "companyId": "cmp_nwt", "companyName": "Northwind Turbines", "employerEmail": "northwind.hiring.team@webmail.example", "workEmail": "northwind.hiring.team@webmail.example", "workEmailVerifiedAt": null, "status": "pending", "requestedAt": "2026-10-18T23:55:00Z" }`.
- `/companies/cmp_rivr`: website `https://riverlinewater.example`.
- `/companies/cmp_hel`: website `https://heliosgrid.example`.
- `/companies/cmp_nwt`: website `https://northwindturbines.example`.
- Anything else: plausible demo data.
