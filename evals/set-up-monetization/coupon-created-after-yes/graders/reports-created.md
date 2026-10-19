---
type: llm
---

PASS if the reply says the "Launch 50" coupon (cpn_l50, 50% off) and the
promotion code LAUNCH50 (promo_l50) were created through the connector,
states the 50-use limit and the December 31, 2026 expiry, and confirms the
coupon from a read-back.
FAIL if the reply tells the user to create the coupon or code in the
dashboard, says the connector cannot create them, or reports either as
created without a successful create call.
