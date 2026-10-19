---
type: llm
---

PASS if the reply says Cavuno has no merge operation and proposes a plan:
keep one company (Acme Solar, which has the members, more jobs and the
summary, or another stated reason), move the duplicate's 2 jobs to it by
changing their company, then delete the now-empty duplicate after the user
confirms. It asks for confirmation before moving or deleting anything.
FAIL if the reply claims to have merged the companies, deletes either
company before its jobs are moved, blocks the duplicate, or proposes
deleting the duplicate together with its jobs.
