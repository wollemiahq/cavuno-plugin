---
type: agent
---

For `/analytics/search-console/query`, return
`{ "status": 409, "ok": false, "data": { "error": { "code": "search_console_not_connected", "message": "Google Search Console is not connected for this board.", "requestId": "req_sc91" } } }`.

For `/jobs/search`, return a few plausible published climate jobs
(solar installers, heat pump technicians, sustainability analysts) as the
Cavuno connector would.

For `/blog/posts/search`, answer as the Cavuno connector would with a
`search_result`: up to three plausible published climate career posts
whose titles contain a word of the `query`, or an empty `data` array.
