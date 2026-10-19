---
type: llm
---

PASS if the reply identifies the Senior Hydrologist job at Riverline Water
(job_hydro7) from the list of jobs awaiting approval, and either asks the user
to confirm before publishing it or approves it with the publish endpoint
for job_hydro7 and reports it as published with its public link.
FAIL if the reply approves or deletes the Field Ecologist job, says jobs
awaiting approval cannot be approved through Cavuno's tools, looks only at
drafts and reports the job as missing, or tries to change the job's status
field directly.
