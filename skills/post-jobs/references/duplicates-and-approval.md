# Duplicates, publishing and approval

## Contents

- Check before writing
- When create answers 409
- Retries after a timeout
- Publishing and approving
- What moderation means for approval

## Check before writing

Run these reads for every job, in this order, and stop at the first match:

1. `read` `/jobs` with `{ externalId: "<id>" }`. A match is the same job.
2. `read` `/jobs` with `{ applicationUrl: "<url>" }`. Cavuno normalizes
   casing and tracking parameters, so equivalent links match. Compare
   titles: the same title and company is the same job; a different title is
   probably a different role sharing one ATS page or inbox.
3. Within a pasted list, compare rows with each other by company and title
   before sending a batch.

Same job already on the board: propose `update` PATCH `/jobs/{id}` with the
changed fields only, or skip it. Expired match: propose
`/jobs/{id}/publish` or a fresh draft, and say which.

## When create answers 409

`POST /jobs` (and a create inside `/jobs/batch`) returns 409
`jobs_already_exists` with `details.existing_job_id` and
`details.matched_on`.

| `matched_on` | Decision |
| --- | --- |
| `external_id` | Same job. Read `existing_job_id` and offer an update. Never retry the create. |
| `composite_hash` | Same company, title, location and description. Same job; report it as a duplicate. |
| `application_url` | Read `details.matching_jobs` (up to five `{ id, title, status }`). If every matching title is a different role, resend the same body plus `allowSharedApplicationUrl: true` with a new `idempotencyKey`. If one looks like the same role, stop and ask the user. |

The retry must resend every field from the first request; the flag skips
only the application URL check, so a matching `externalId` or identical
job still returns 409. Tell the user the job shares its application
destination with the listed jobs.

In a batch, a 409 is one entry's `error`; the rest of the batch still ran.
Retry only those entries, as a new batch.

## Retries after a timeout

Send a fresh `idempotencyKey` with every create, as `cavuno-basics`
describes. After a timeout or a 5xx, retry the identical request with the
same key. If the first attempt went without a key, `read` `/jobs` with the
job's `externalId` before retrying; if the job exists, read it back and
continue instead of creating it again.

## Publishing and approving

- `create` `/jobs/{id}/publish` publishes a `draft` or `expired` job and
  approves a `pending_approval` job. Optional body `{ expiresAt }` takes an
  ISO 8601 datetime; `null` clears an explicit expiry; omit it to keep the
  current one.
- 409 from publish means the job is already published or the plan's
  published-job quota is used up. Read `/usage` and tell the user which.
- Reject a pending job with `delete` `/jobs/{id}`. It cannot be undone and
  needs its own confirmation.
- `/jobs/{id}/pause` returns a published job to draft.
- List the approval queue with `read` `/jobs` and
  `{ status: "pending_approval" }`; a `draft` filter does not include them.

## What moderation means for approval

- Jobs land in `pending_approval` only when the board's "Require job
  approval" setting holds free submissions, aggregated (backfilled) jobs or
  both. Paid posts, imports and operator-created jobs are not held.
- The expiry countdown of a free or aggregated job starts at approval, so a
  job that waited in the queue still gets the full duration.
- Approving counts against the active job limit. Read `/usage` before
  approving several.
- Rejecting a free submission emails the poster that it was not approved.
  A rejected aggregated job is not imported again.
- Aggregated jobs still awaiting approval 30 days after they arrived are
  removed automatically.

Sources: https://cavuno.com/docs/mcp/reference/jobs,
https://cavuno.com/docs/managing-jobs/job-moderation,
https://cavuno.com/docs/managing-jobs/job-posting-workflow,
https://cavuno.com/docs/api
