# Subscribers, alerts and marketing consent

## Contents

- Contacts: everyone the board can email
- Subscriber states
- Counting subscribers
- Look up one person
- Unsubscribe and resubscribe
- Marketing consent
- What Cavuno sends on its own
- Dashboard-only tasks

## Contacts: everyone the board can email

`/contacts` lists each person once, whatever they get: job alerts, job
matches or marketing email. Use it for "who can my board email", for one
person's whole picture, and for "stop everything". `/subscribers` below is
the job alert view only.

| Call | Returns |
| --- | --- |
| `read` `/contacts` | Newest first. Filters: `status` (`subscribed`, `pending`, `waitlisted`, `unsubscribed`, `suppressed`), `subscription` (`job_alerts`, `job_matches`, `marketing`), `role`, `source`, `search` (email substring). |
| `read` `/contacts/{id}` | `status`, `suppressionReason` (`hard_bounce`, `spam_report`, `blocked`), `subscriptions[]` (`kind`, `status`, `since`, marketing's `consentText`), `source`, `subscribedAt`, `lastEmail`. |
| `read` `/contacts/{id}/emails` | What was sent and whether it was delivered, delayed or bounced, with the reason. Opens and clicks are never recorded. |
| `read` `/contacts/{id}/activity` | Signed up, confirmed, waitlisted, unsubscribed, suppressed, with dates. |
| `read` `/contacts/{id}/job-alerts` | Each alert's filters and frequency. |

Writes need `alerts.manage`. Confirm each one by email address first, then
read `/contacts/{id}` back.

| Action | Call | Effect |
| --- | --- | --- |
| Stop everything | `create` `/contacts/{id}/unsubscribe` | Job alerts, job matches and marketing all off, recorded as the team's. |
| Turn one off | `delete` `/contacts/{id}/subscriptions/{kind}` | That stream only. Job alerts stay saved but stop; marketing is withdrawn as `operator_request`. |
| Delete one alert | `delete` `/contacts/{id}/job-alerts/{alertId}` | Permanent; the person's other alerts keep sending. |
| Bring back | `create` `/contacts/{id}/resubscribe` | `outcome: resubscribed` when the team made every unsubscribe and the address is confirmed: everything comes back at once. Otherwise, when job alerts are off, `confirmation_sent` (or `confirmation_send_failed`): whatever the team turned off comes back now, the job alerts come back when the person clicks the emailed link, and anything the person turned off stays off. 409 `contacts_not_resubscribable` when only job matches or marketing the person turned off themselves are off (only they can turn those back on), or the contact isn't unsubscribed. 429 `contacts_confirmation_throttled` when a confirmation went out in the last 2 minutes. |

Nobody can add a subscription for a person. Only their own opt-in does.
The same commands exist in the CLI as `cavuno contacts` (`list`, `get`,
`emails`, `activity`, `job-alerts`, `unsubscribe`, `remove-subscription`,
`remove-job-alert`, `resubscribe`).

## Subscriber states

| `status` filter | Dashboard label | Meaning |
| --- | --- | --- |
| `unconfirmed` | Pending confirmation | Signed up, has not opened the confirmation link; gets no digests. |
| `confirmed` | Active | Confirmed and within the plan's Email subscribers allowance; gets the weekly digest when new jobs match. |
| `waitlisted` | Waitlisted | Confirmed, but the allowance is full; queued until a slot frees. |
| `unsubscribed` | Unsubscribed | Opted out or stopped by an administrator. |

A subscriber record has no `status` field: its state shows in
`confirmedAt`, `waitlistedAt` and `unsubscribedAt`. Use `status` only to
filter `GET /subscribers`. Imported subscribers are confirmed without a
confirmation email.

## Counting subscribers

- `GET /subscribers/count` returns `confirmed`: everyone who has ever
  confirmed. It is not reduced by unsubscribes, so it can exceed the number
  receiving email today. Label it "confirmed sign-ups (all time)".
- For the current number of active subscribers, page `GET /subscribers`
  with `status: confirmed` (`limit` up to 100, follow `nextCursor`) and
  count rows, without printing them. Only do this when the user needs the
  current figure; say how many pages it took.
- Plan allowance: `read` `/usage`, capacity `confirmed_subscribers`
  (`used`, `limit`, `remaining`). Live job-alert and matching-email streams
  count toward it; waitlisted people do not.

## Look up one person

1. `read` `/subscribers` with `search` (substring of the email).
2. If exactly one row matches the email the user gave, `read`
   `/subscribers/{id}`: `confirmedAt`, `waitlistedAt`, `unsubscribedAt`,
   `lastSentAt` and `alerts` (each with
   `filters`, `frequency` `weekly`, `isActive`, `lastSentAt`).
3. If several rows match, show only those rows' emails and statuses and ask
   which one.

## Unsubscribe and resubscribe

| Action | Call | Effect |
| --- | --- | --- |
| Unsubscribe | `create` `/subscribers/{id}/unsubscribe` | Sets `unsubscribedAt`, deactivates the person's alerts; stops job alerts and matching emails. The next waitlisted person can be promoted. |
| Resubscribe | `create` `/subscribers/{id}/resubscribe` | Undoes an unsubscribe the team made: clears `unsubscribedAt` and reactivates alerts; waitlisted if the allowance is full. A 409 `subscribers_confirmation_required` means the person unsubscribed themselves or never confirmed: use `/contacts/{id}/resubscribe`, which emails them a confirmation link. |

Both need `alerts.manage`. Confirm each one by email address, then read
`/subscribers/{id}` back and report `unsubscribedAt`. Never resubscribe
someone who asked to stop; only on their own request.

## Marketing consent

- `GET /marketing-permissions` lists recorded decisions (`status`
  `granted` or `withdrawn`, `source`, `reason`, dates). `email` is an exact
  match returning at most one record.
- No record means no consent, never a default yes.
- `create` `/marketing-permissions/{id}/withdraw` (`{id}` is the board user
  id from the record) records `reason: operator_request`. Use it when a
  person asks the operator directly to stop marketing email. Re-withdrawing
  changes nothing.
- Withdrawal and unsubscribing are separate records. For "stop all emails",
  use `/contacts/{id}/unsubscribe`, which does both.

## What Cavuno sends on its own

- A confirmation email at signup, and a weekly digest per confirmed
  subscriber when new jobs match their filters (no email if nothing
  matches).
- A weekly job recommendation digest for people who opted into matching
  emails.
- Every digest has an unsubscribe link for its stream.
- Cavuno does not send manual or bulk emails to the list.

## Dashboard-only tasks

Export the list (Email subscribers → **...** → Export), import a CSV, resend a
confirmation email, and edit email templates (Email editor).

Sources: https://cavuno.com/docs/job-alerts,
https://cavuno.com/docs/job-alerts/manage-subscribers,
https://cavuno.com/docs/job-alerts/email-notifications,
https://cavuno.com/docs/job-alerts/email-deliverability,
https://cavuno.com/docs/billing/plans-and-pricing
