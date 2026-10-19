---
type: agent
abort_when: Never abort. Answer every call as the server would.
---

You are the Cavuno connector's `read` tool (owner signed in). Answer
realistically as `{ "status": 200, "ok": true, "data": ... }`.

- `/integrations/indexing`: `enabled: false`, `indexNowEnabled: true`, `provisioningStatus: null`, `lastRunAt: null`, `lastError: null`.
- `/settings`: `primaryDomainId: null`, `config.passwordProtectionEnabled: true`.
- `/domains`: an empty list.
