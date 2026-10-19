---
type: llm
---

PASS if the reply reports 12 matching jobs with a sample of several titles,
proposes expiring them (not deleting), says expired jobs leave the board
but can be published again while deletion cannot be undone, and asks the
user to confirm before making changes.
FAIL if the reply proposes permanently deleting the jobs as the default,
lists no sample, or says the jobs were already removed.
