---
name: connect-and-check-board
description: Confirms the Cavuno connector is signed in to the right job board and summarizes the board's health without changing anything - who is signed in and with what role, the board name, domain and password-protection status, usage against plan limits, jobs and employer claims waiting for approval, and indexing problems. Also explains what the connector cannot do, fixes sign-in trouble and reports Cavuno problems. Use when the user says "is Cavuno connected?", "check my board", "which board am I signed in to?", "how close am I to my job limit?", "why can't you do that on Cavuno?" or runs /cavuno:check.
---

# Connect and check a board

Follow the `cavuno-basics` skill for tool use, confirmations and read-back.

## When to use / not

Use this skill at the start of a session, for a health check, for sign-in
errors and for "can the connector do X" questions. It only reads.

Hand off when the user wants something changed:

- launch steps, settings, domains, password protection: `launch-and-set-up-board`
- approving, expiring or cleaning up jobs, team access, indexing toggles: `tidy-board`
- traffic, views, apply clicks, Search Console: `review-performance`
- employer claims and company accounts: `manage-employers`
- employer pricing and Stripe: `set-up-monetization`

## Steps

1. **Sign-in.** `read` `/me`. Report `board.name`, `board.slug`,
   `actor.type` (`oauth_token`, `api_key` or `user_session`) and
   `actor.role` (`owner`, `admin` or `member`). `role` is `null` for an API
   key; that is normal. On 401, stop and give the reconnect steps in
   [references/connector-limits.md](references/connector-limits.md). If the
   board is not the one the user meant, say so before any other read.
2. **Board state.** `read` `/settings`. Note `name`, `primaryDomainId` and
   from `config`: `passwordProtectionEnabled`, `requireApprovalFreeJobs`,
   `requireApprovalAggregatedJobs`, `jobAlertsEnabled`.
3. **Domains.** `read` `/domains`. For each domain report `domain`,
   `status` (quote it as returned), `isPrimary`, `statusReason` and
   `verifiedAt`. Match `primaryDomainId` to a row.
4. **Usage and plan.** `read` `/usage`. For each entry in `capacities`
   (`active_jobs`, `confirmed_subscribers`, `team_seats`) show
   `used / limit` and `remaining`; `limit: null` means unlimited. Then
   `read` `/billing/subscription` for `planKey`, `status`, `active`,
   `cancelAtPeriodEnd` and `currentPeriodEnd` (milliseconds since epoch;
   convert to a date).
5. **Waiting on the operator.**
   - `read` `/jobs` with `{ status: "pending_approval", limit: 100 }` and
     follow `nextCursor` to count. Stop after three pages and report
     "300+" rather than paging further.
   - `read` `/employers/claims` (defaults to `pending`) and count the same
     way.
6. **Indexing.** `read` `/integrations/indexing`. Note `enabled` (Google
   Indexing API), `indexNowEnabled`, `provisioningStatus` and `lastError`.
7. **Flag what needs attention** using the rules in
   [references/health-checks.md](references/health-checks.md). Only flag
   what a read actually showed.
8. **Write the summary** in the shape of
   [examples/health-summary.md](examples/health-summary.md): one line per
   area, attention items first, then at most three next steps, each naming
   the skill or dashboard page that handles it.

If the user gave a focus (for example "usage" or "domains"), run step 1 and
the steps for that focus only.

Done when:

- [ ] `/me` was read and the board name, sign-in type and role are stated.
- [ ] Each area in steps 2 to 6 shows a value or "couldn't check" with the
      error code and `requestId`; no failed read is shown as zero or "none".
- [ ] Every attention item cites the field that triggered it.
- [ ] No `create`, `update` or `delete` call was made.
- [ ] The reply ends with at most three next steps.

## Guardrails

- **Read only.** If the user asks to fix something mid-check, finish the
  summary, then switch to the skill that owns the fix.
- **403 on one area** means the role cannot see it (a `member` cannot open
  settings, domains, integrations or the team). Write "not visible to your
  role" for that line and carry on.
- **People stay counts.** Report pending claims and team seats as numbers.
  Do not list claimant or member emails in the summary.
- **Never ask for an API key in chat.** The connector signs in with OAuth.
  API keys are for scripts and the code-mode endpoint and belong in the
  client's configuration, not in a message.
- **Report Cavuno problems.** When a read fails in a way the docs do not
  explain, or the user needs a capability that does not exist, call
  `submit_feedback` (type `unexpected_response` or `missing_capability`)
  with the goal and the error code, then tell the user the next step.

## References

- [references/health-checks.md](references/health-checks.md): each check, the
  fields to read, what counts as healthy, and who fixes it.
- [references/connector-limits.md](references/connector-limits.md): sign-in
  troubleshooting, what the connector cannot do and where to send the user.
- [examples/health-summary.md](examples/health-summary.md): a finished
  health summary.
