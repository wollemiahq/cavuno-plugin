# Example: board health summary

## Input

"Check my Cavuno board."

## Plan

Read `/me`, `/settings`, `/domains`, `/usage`, `/billing/subscription`,
`/jobs?status=pending_approval`, `/employers/claims` and
`/integrations/indexing`. No writes.

## What the reads returned (abridged)

- `/me`: board "Climate Careers" (`climate-careers`), `oauth_token`, role
  `owner`.
- `/settings`: `passwordProtectionEnabled: false`,
  `requireApprovalFreeJobs: true`.
- `/domains`: `jobs.climatecareers.example`, `status: "active"`,
  `isPrimary: true`.
- `/usage`: `active_jobs` 482 of 500 (18 left), `confirmed_subscribers` 900
  of 1,000, `team_seats` 3 of 5.
- `/jobs?status=pending_approval`: 2 jobs. `/employers/claims`: failed, 503,
  `requestId` `req_91b2`.
- `/integrations/indexing`: `enabled: true`, `indexNowEnabled: true`,
  `lastError: null`.

## Output

**Climate Careers** - signed in as owner (OAuth).

Needs attention

- 2 jobs are waiting for your approval. Approving both leaves 16 active-job
  slots.
- Active jobs: 482 of 500 used. 18 slots left before publishing stops.
- Employer claims: couldn't check (503, request `req_91b2`). This is not a
  zero.

Looks fine

- Domain: jobs.climatecareers.example is active and primary.
- Public: password protection is off.
- Plan: Growth, active, renews on 12 November 2026.
- Subscribers: 900 of 1,000. Team seats: 3 of 5.
- Indexing: Google Indexing API and IndexNow are on, no errors.

Next steps

1. Review the 2 pending jobs (I can do that with you: "tidy my board").
2. Free up active-job slots by expiring closed roles before your next
   import.
3. Ask me to check employer claims again in a few minutes.
