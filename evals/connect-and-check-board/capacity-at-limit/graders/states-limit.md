---
type: llm
---

PASS if the reply says active jobs are at the limit (500 of 500, none
left), says subscribers are unlimited (not zero), and suggests freeing slots
by expiring closed or stale jobs or changing plan in the Cavuno dashboard.
FAIL if the reply says subscribers have 0 remaining, claims there is room
for more active jobs, or tries to start a plan upgrade or checkout itself.
