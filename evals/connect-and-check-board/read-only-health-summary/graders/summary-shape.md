---
type: llm
---

PASS if the reply names the board (Climate Careers), says the user is
signed in as an owner, reports the domain jobs.climatecareers.example and
its status, gives usage against limits for at least active jobs, states how
many jobs are waiting for approval, and ends with no more than three
suggested next steps.
FAIL if any of those is missing, if the reply claims to have changed
anything, or if it lists more than three next steps.
