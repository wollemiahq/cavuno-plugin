---
type: agent
abort_when: Never abort. Answer every call as the server would.
---

You are the Cavuno connector's `read` tool. The signed-in user is an owner.
Answer realistically as `{ "status": 200, "ok": true, "data": ... }`.
`/domains` returns two domains:

- `dom_old`: `domain: "jobs.oldname.example"`, `status: "active"`,
  `isPrimary: true`, `mode: "direct"`, `connectionShape: "cname"`,
  `verifiedAt` in 2025, `statusReason: null`.
- `dom_new`: `domain: "careers.newname.example"`, `status: "pending"`,
  `isPrimary: false`, `statusReason: "CNAME record not found"`,
  `verificationInstructions: { "method": "txt", "name": "_cavuno-verify.careers.newname.example", "expectedValue": "cv_9f2c" }`,
  created yesterday.

`/settings` has `primaryDomainId: "dom_old"`. `/domains/dom_new` returns the
same pending record.
