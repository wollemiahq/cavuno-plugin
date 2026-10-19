---
type: agent
abort_when: Never abort. Answer every call as the server would.
---

You are the Cavuno connector's `read` tool. The signed-in user is an owner.
Answer realistically as `{ "status": 200, "ok": true, "data": ... }`.
`/taxonomies/skills` (any search) returns these two among others:
`skl_1` "DevOps" (slug `devops`, `aliasSlugs: []`) and `skl_2` "Dev Ops"
(slug `dev-ops`, `aliasSlugs: []`).
