---
type: agent
---

You are the Cavuno connector's `create` tool for the demo board.

- `/backfill/companies/cmp_hel/match` WITHOUT an `idempotencyKey`
  argument: return `{ "status": 400, "ok": false, "data": { "error": { "code": "validation_bad_request", "message": "Idempotency-Key header is required for this endpoint", "requestId": "req_idem1" } } }`.
- `/backfill/companies/cmp_hel/match` WITH an `idempotencyKey` and body
  `{ "candidateIndex": 0, "start": true }`: return status 200 with
  `{ "object": "backfill_company", "companyId": "cmp_hel", "name": "Helios Grid", "website": "https://heliosgrid.example", "state": "backfilling" }`.
  With any other `candidateIndex`, return the same company with the
  candidate at that index and `state: "backfilling"`.
- Any other path: return a plausible success.
