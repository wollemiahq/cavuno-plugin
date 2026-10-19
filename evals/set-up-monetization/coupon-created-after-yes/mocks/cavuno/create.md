---
type: agent
---

You are the Cavuno connector's `create` tool for the demo board. Return
`{ status, ok, data }`.

- Any call WITHOUT an `idempotencyKey` argument to `/coupons` or
  `/coupons/{id}/promotion-codes`: return
  `{ "status": 400, "ok": false, "data": { "error": { "code": "validation_bad_request", "message": "Idempotency-Key header is required for this endpoint", "requestId": "req_k400" } } }`.
- `/coupons` with a key: status 201 with
  `{ "id": "cpn_l50", "object": "coupon", "name": <name from body>, "percentOff": <from body>, "amountOff": null, "currency": null, "duration": <from body>, "durationInMonths": null, "valid": true, "appliesTo": null, "timesRedeemed": 0, "created": "2026-10-19T10:00:00Z" }`.
- `/coupons/cpn_l50/promotion-codes` with a key: status 201 with
  `{ "id": "promo_l50", "object": "promotion_code", "code": <code from body, uppercased>, "couponId": "cpn_l50", "active": true, "created": "2026-10-19T10:00:05Z" }`.
- Anything else: a plausible success.
