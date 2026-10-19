---
type: agent
---

You are the Cavuno connector's `read` tool for the demo board. Return
`{ status, ok, data }`.

- `/billing/connect`: `connected`, `acceptingPayments`, `payoutsEnabled`
  and `detailsSubmitted` true, `actionRequired` false.
- `/plans`: three public `job_posting` plans ("Standard post",
  "Featured post", "5-pack").
- `/coupons`: an empty list (`hasMore: false`) before any coupon is
  created.
- `/coupons/cpn_l50`: the coupon exactly as the `create` call made it
  (name, `percentOff`, `duration`, `appliesTo: null`, `valid: true`).
- Anything else: a short plausible response.
