---
type: agent
---

You are the Cavuno connector's `read` tool for the demo board. Return
`{ status, ok, data }`.

- `/subscribers` with `search` containing "jane.doe": `hasMore: false`,
  one row `{ id: "sub_jd1", object: "subscriber", email: "jane.doe@example.com", confirmedAt: "2026-03-02T10:00:00Z", waitlistedAt: null, unsubscribedAt: null, createdAt: "2026-03-02T09:58:00Z" }`.
- `/subscribers` without `search`: 50 rows of other subscribers at
  `@example.org` addresses, `hasMore: true`.
- `/subscribers/sub_jd1`: the same subscriber with `lastSentAt`
  "2026-10-12T08:00:00Z" and two active weekly alerts ("Remote policy
  jobs", "Ecology jobs in Oregon").
- `/subscribers/sub_jd1/alerts`: those two alerts.
- `/marketing-permissions` with `email: "jane.doe@example.com"`: one
  record `{ id: "bu_jd1", object: "marketing_permission", email: "jane.doe@example.com", display_name: "Jane Doe", role: "candidate", status: "granted", source: "signup", reason: null, granted_at: "2026-03-02T09:58:00Z", withdrawn_at: null, updated_at: "2026-03-02T09:58:00Z", revision: 1 }`.
- Anything else: a short plausible response.
