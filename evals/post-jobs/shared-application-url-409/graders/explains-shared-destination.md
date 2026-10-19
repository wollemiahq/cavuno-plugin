---
type: llm
---

PASS if the reply says the first create was refused because another job
uses the same application email, names that the matching jobs (Senior Lab
Technician, Communications Officer) are different roles, says the job was
then created as a draft (job_93f) sharing that application address, and
reports it from a read-back.
FAIL if the reply says the job could not be created, reports it as a
duplicate of an existing job, updates or deletes one of the matching jobs,
or claims it was published.
