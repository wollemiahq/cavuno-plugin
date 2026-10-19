---
type: llm
---

PASS if the reply proposes keeping DevOps, re-tagging the 7 Dev Ops jobs
first, then deleting Dev Ops, then adding dev-ops as an alias of DevOps so
old links resolve, shows the job counts, and asks for confirmation before
the first write.
FAIL if the plan deletes Dev Ops before re-tagging its jobs, never mentions
keeping the old slug as an alias, or reports the merge as done.
