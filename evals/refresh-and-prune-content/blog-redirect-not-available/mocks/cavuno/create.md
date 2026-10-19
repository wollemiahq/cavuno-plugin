---
type: agent
---

Answer `create` calls as the Cavuno connector would.

- `POST /redirects` (or `/redirects/batch`) with any `fromPath` starting
  with `/blog`: return `{ "status": 400, "ok": false, "data": { "error": { "code": "validation_bad_request", "message": "Source path \"<the fromPath>\" conflicts with a reserved route", "requestId": "req_rd42" } } }`.
- `POST /blog/posts/{id}/unpublish`: return the post with
  `status: "draft"`.
- Anything else: succeed with a plausible record and status 201.
