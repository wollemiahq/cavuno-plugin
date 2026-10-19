# Job review rules

## Contents

- Job states and actions
- Pending approval
- Stale jobs
- Duplicates
- Spam and off-niche jobs
- Batch shape

## Job states and actions

`status` is one of `draft`, `published`, `expired`, `archived`,
`pending_approval`.

| Action | Call | Result | Reversible |
| --- | --- | --- | --- |
| Approve or publish | `POST /jobs/{id}/publish` (optional `expiresAt`, ISO 8601) | Live; counts against `active_jobs`; without an explicit expiry it gets `defaultJobDurationDays` (30 when unset) from publication | Yes |
| Pause | `POST /jobs/{id}/pause` | Back to `draft`, off the public board | Publish again |
| Expire | `POST /jobs/{id}/expire` | `expired`, `expiresAt` set to now; leaves listings, search and the sitemap within minutes; URL returns 404 with no `JobPosting` data | Publish again |
| Edit | `PATCH /jobs/{id}` | Only sent fields change | Yes |
| Delete or reject | `DELETE /jobs/{id}` | Gone for good, with saved jobs, orders and search entries | No |

The dashboard's "Unpublish" is the API's pause. Google Indexing API and
IndexNow, when on, are told about publishes, edits, expiries and removals.

## Pending approval

Free submissions and aggregated (backfill) jobs wait here when the matching
`requireApproval*` setting is on. Paid posts, imports, feeds and jobs created
by the operator never wait.

Review each job's detail. Recommend **reject** when:

- the application URL or email belongs to a different company than the job
  names;
- the location contradicts the description;
- it duplicates a live job (see below);
- it is off the board's niche, or reads as spam (no identifiable employer,
  or it asks applicants to pay).

Recommend **edit then approve** for fixable issues (typo in title, missing
salary the description states). Otherwise **approve**.

Timing facts to tell the user: approval starts the expiry clock, so the
employer gets the full duration; an aggregated job still pending 30 days
after it arrived is removed automatically, and earlier if the source closes
it; free submissions wait until someone acts. Once an aggregated job is
approved, later updates from its source do not need approval again.

Approve in order of the user's priority, and stop when `active_jobs`
`remaining` reaches 0; report how many did not fit.

## Stale jobs

A published job is stale when it is older than the age the user chose and
the role may have closed. Backfilled, imported and feed jobs, and jobs the
operator added, expire automatically when the source posting disappears;
paid public submissions are left for the operator to expire, so list those
separately and suggest asking the employer.

Default action: expire. Use pause when the employer says hiring is on hold.
Extend the expiry only when the same role is confirmed still open.

## Duplicates

Signals, strongest first:

1. Same `externalId`.
2. Same `applicationUrl` (the API normalizes casing and tracking
   parameters; check with `GET /jobs?applicationUrl=`). A shared ATS page or
   inbox can be legitimate for different roles, so also compare titles.
3. Same company and title after lowercasing and removing punctuation,
   with overlapping locations.

Keep the copy that is published, has the fuller data (salary, location,
description), and the older URL. Expire the others. If both are drafts or
pending, delete or reject the extra after its own confirmation.

## Spam and off-niche jobs

For a company that keeps sending spam, `POST /companies/{id}/block` stops
automated sourcing from it and archives its published jobs; `remove: true`
also takes down its profile and jobs. That belongs to `manage-employers`;
propose it, do not run it from a tidy without the user asking.

## Batch shape

```json
{ "operations": [
  { "id": "1", "method": "POST", "action": "expire", "resourceId": "job_123" },
  { "id": "2", "method": "POST", "action": "publish", "resourceId": "job_456" },
  { "id": "3", "method": "PATCH", "resourceId": "job_789", "body": { "salaryMin": 60000 } },
  { "id": "4", "method": "DELETE", "resourceId": "job_999" }
] }
```

Up to 100 operations, unique `id`s. The response is HTTP 200 even when
entries fail; each `data` entry carries its own result. Keep deletes in
their own batch so one confirmation does not cover both.

Sources: https://cavuno.com/docs/managing-jobs/job-moderation,
https://cavuno.com/docs/managing-jobs/edit-delete-jobs,
https://cavuno.com/docs/job-board-seo/expired-job-handling,
https://cavuno.com/docs/mcp/reference/jobs,
https://cavuno.com/blog/job-posting-schema
