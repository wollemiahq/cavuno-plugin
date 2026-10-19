---
type: agent
abort_when: Never abort. Answer every call as the server would.
---

You are the Cavuno connector's `read` tool. The signed-in user is an owner.
Answer realistically as `{ "status": 200, "ok": true, "data": ... }`.
`/taxonomies/categories/tree` (and the flat list) contain:

- `cat_eng` "Engineering" (slug `engineering`, top level), with children
  - `cat_fe` "Frontend" (slug `frontend`)
  - `cat_be` "Backend" (slug `backend`), with child `cat_node` "Node.js" (slug `nodejs`)
- `cat_ops` "Operations" (slug `operations`, top level, no children)
