# Sign-in and connector limits

## Connect or reconnect

| Client | Steps |
| --- | --- |
| Claude on the web or desktop | Customize → Connectors → + Add → Add custom connector, name it Cavuno, URL `https://mcp.cavuno.com/mcp`, then sign in to Cavuno and approve access. Whether a member can add a custom connector depends on the Claude plan and organization settings. |
| Claude Code | `claude mcp add --transport http cavuno https://mcp.cavuno.com/mcp`, then run `/mcp`, pick Cavuno and sign in. |
| Cursor, Codex, ChatGPT developer mode | Add `https://mcp.cavuno.com/mcp` as a remote MCP server; the client opens a browser to sign in. |

The Cavuno dashboard has the server URL under Settings → Integrations → MCP.
No API key is needed for this sign-in. Cavuno grants the single
`full_access` scope, but every call is still checked against the user's
current role on the board.

## Sign-in problems

| Symptom | Cause | What to tell the user |
| --- | --- | --- |
| 401 on every call | Not signed in, or the sign-in expired | Reconnect Cavuno with the steps above |
| `/me` shows another board | The credential is bound to the board approved at sign-in | Disconnect and reconnect, approving access for the intended board |
| 403 on settings, domains, team or integrations | Role is `member` (Editor) | Ask an owner or admin to change the role, or to run the task |
| 403 on one write, with a plan message | The plan does not include it | Say which feature; plan changes happen in Settings → Billing |
| Calls work but the board looks empty | Possibly the wrong board | Check `board.slug` in `/me` before concluding anything |

API keys (Settings → Developer → API keys) are for scripts, CI and the
code-mode endpoint. A key holds only the permissions chosen when it was
made, and `/me` shows `role: null` for it. Keys go in the client's
configuration, never into a chat message.

## What the connector cannot do

| Request | Where it goes |
| --- | --- |
| Bulk exports (jobs, companies, candidates, subscribers, blog) | Dashboard export, or the code-mode endpoint |
| Resume and operation-file downloads | Dashboard |
| CSV or XML job imports, subscriber imports, redirect CSV imports | Dashboard (Jobs → Import, Job alerts, Settings → Redirects) |
| Image upload from plain chat | Dashboard; Claude Code can use the media upload tools |
| Stripe Connect onboarding, tax, ads | Dashboard |
| Paying for a plan | Cavuno returns a hosted checkout link; the user completes payment |
| "Who changed this?" | There is no audit-log API on the connector |
| Browsing a careers page or another website | Another connector or the client's own web tools |
| Search Console live tests and indexing requests | Search Console itself; the connector only reads finalized performance and indexed URL inspection |
| Website design, pages and navigation | AI website builder in the dashboard |

Writes that require an `Idempotency-Key` (backfill rules and company
actions, coupons and promotion codes, plan prices, the candidate paywall,
sales-led plans, billing upgrade) do work through the connector: pass the
`idempotencyKey` argument as `cavuno-basics` describes. An upgrade still
returns a hosted link for the user to pay.

## Reporting a problem

Call `submit_feedback` when Cavuno itself got in the way: a missing
capability, a wrong or unclear doc, a surprising response or an unhelpful
error. It needs no confirmation. Required: `type`, `goal`, `message`; add
`endpoint`, `outcome` and `request_id` when known. Each account can send 20
reports every 5 minutes. Leave out credentials, personal data and the
user's content.

Support for a person: hi@cavuno.com, with the board URL, the time, what was
attempted and any `requestId`. Never include passwords or API keys.

Sources: https://cavuno.com/docs/mcp,
https://cavuno.com/docs/mcp/reference/authentication-and-security,
https://cavuno.com/docs/mcp/reference/limitations,
https://cavuno.com/docs/mcp/reference/usage-and-limits,
https://cavuno.com/docs/troubleshooting
