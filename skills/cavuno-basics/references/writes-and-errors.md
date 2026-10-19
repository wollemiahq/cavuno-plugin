# Writes, duplicates, batches and errors

## Contents

- Idempotency keys
- Duplicate jobs
- Batches
- Async operations
- Errors
- Rate limits and capacity

## Idempotency keys

The API accepts an `Idempotency-Key` header on writes so a retried request
runs once. On the connector, pass it as the `idempotencyKey` argument of
`create`, `update` or `delete`; on the code-mode endpoint, pass it in
`cavuno.request({ headers })`.

- Generate a fresh key for each distinct request: letters, digits, hyphens
  and underscores, 255 characters at most, for example
  `coupon-launch20-7f3k9q`.
- Reuse a key only to retry the identical request after a timeout or 5xx.
  A changed body needs a new key.
- If a retry without a key is unavoidable, read the list first (for jobs,
  filter by `externalId`) and retry only if the first attempt did not land.

Some operations require the key and answer 400 `validation_bad_request`
with "Idempotency-Key header is required for this endpoint" without it:
backfill rules (create, update, delete) and backfill company start, stop,
match, mark-no-match, bulk-start and bulk-stop; coupons and promotion codes;
`PUT /plans/{id}/price`; `PUT /paywall`; sales-led plans (create, update,
publish, hide, archive, reorder); and `POST /billing/upgrade`. Always send
one for these. On a batch, the key covers the batch request as a whole, not
each sub-operation.

## Duplicate jobs

`POST /jobs` answers 409 `jobs_already_exists` when the new job matches one
already on the board. `details.existing_job_id` is the match and
`details.matched_on` says why:

| `matched_on` | Matches when | What to do |
| --- | --- | --- |
| `external_id` | Another job has the same `externalId`. | Update the existing job instead. |
| `application_url` | Another job uses the same application URL or email. | Check `details.matching_jobs` (up to five `{ id, title, status }`). If the new job is a different role, resend the full body with `allowSharedApplicationUrl: true` and a new `idempotencyKey`. |
| `composite_hash` | Same company, title, location and description. | It is the same job; do not create it. |

Only the application URL check can be overridden. Always set `externalId`
to the source job URL or ATS requisition ID so reposts are caught.

## Batches

`POST /jobs/batch` runs up to 100 sub-operations (create, update, delete,
publish, pause, expire, duplicate). `/companies/batch` and
`/redirects/batch` also take up to 100, and `/blog/posts/batch` up to 50.
A well-formed batch returns HTTP 200 even when items fail, so:

1. Split larger sets into chunks within the limit.
2. Check every entry of the response `data` array for its own status or
   error.
3. Report counts (succeeded, failed, skipped) and list each failure with its
   error code.
4. Retry only the failed items, after fixing their cause.

## Async operations

A 202 response, or a response with an operation ID, means work continues in
the background. Read `/operations/{id}` at a sensible interval (not a tight
loop) until its status is terminal, then read the resource it was meant to
change. An accepted request does not prove the work succeeded. Operations
are deleted 30 days after they finish. `/operations/{id}/cancel` stops one
that is still running.

## Errors

A failed call returns `data.error = { code, message, details?, requestId? }`.

| Status | Meaning | Do this |
| --- | --- | --- |
| 400 `validation_bad_request` | A field or query parameter is wrong; `message` names it. | Re-check the schema with `search`, fix, retry once. |
| 401 | Not signed in, or the sign-in expired. | Ask the user to reconnect Cavuno. |
| 403 | The user's role or plan does not allow it. | Say which permission is missing; do not look for a way around it. |
| 404 | Wrong ID or path. | Look the record up again. |
| 409 | Conflict, such as a duplicate. | Read `details`; see above. |
| 429 | Rate limited. | Wait for `Retry-After`, then continue. |
| 5xx | Cavuno-side failure. | Retry once later; if it repeats, report it with the `requestId`. |

Never present a failed read as "no results". Quote the error code and the
`requestId` when the user needs to contact support.

## Rate limits and capacity

API keys default to 100 requests per minute. Prefer one batch to many single
calls. Before bulk work, `read` `/usage`: it lists each capacity with its
limit and what remains (`null` means unlimited). Plan details are at
`/billing/subscription`.

Sources: https://cavuno.com/docs/mcp/reference/jobs,
https://cavuno.com/docs/mcp/reference/usage-and-limits,
https://cavuno.com/docs/mcp/reference/operations, https://cavuno.com/docs/api
