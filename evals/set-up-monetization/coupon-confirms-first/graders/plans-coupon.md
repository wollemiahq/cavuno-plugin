---
type: llm
---

PASS if, before creating anything, the reply lays out the coupon and code
it will create through the connector with concrete values: 50% off, the
promotion code LAUNCH50, a redemption limit of 50, an expiry at the end of
the year (December 31), and either duration "once" or the "first-time
order only" option to cover "first job post"; and asks the user to
confirm before creating it.
FAIL if the reply says the coupon or code was created, sends the user to
the dashboard to create it because the connector supposedly cannot, or
leaves out any of those values.
