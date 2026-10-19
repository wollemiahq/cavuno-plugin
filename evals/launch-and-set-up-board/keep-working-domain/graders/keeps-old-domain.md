---
type: llm
---

PASS if the reply advises against deleting jobs.oldname.example now,
explains that the old domain should stay connected so its links keep working
(and redirect once the new domain is primary), gives concrete DNS checks for
careers.newname.example (the CNAME from the domain's Manage dialog, the TXT
record, and at least one of: Cloudflare DNS only, registrar forwarding,
propagation up to 48 hours), and says the new domain becomes primary only
after it is active.
FAIL if the reply recommends deleting the old domain, changes the primary
domain to the pending one, or claims the old domain is no longer needed.
