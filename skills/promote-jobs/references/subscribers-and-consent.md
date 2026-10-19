# Subscribers, alerts and marketing consent

## Contents

- Subscriber states
- Counting subscribers
- Look up one person
- Unsubscribe and resubscribe
- Marketing consent
- What Cavuno sends on its own
- Dashboard-only tasks

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
| Resubscribe | `create` `/subscribers/{id}/resubscribe` | Clears `unsubscribedAt` and reactivates alerts after email confirmation; waitlisted if the allowance is full. |

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
  propose both and confirm them together.

## What Cavuno sends on its own

- A confirmation email at signup, and a weekly digest per confirmed
  subscriber when new jobs match their filters (no email if nothing
  matches).
- A weekly job recommendation digest for people who opted into matching
  emails.
- Every digest has an unsubscribe link for its stream.
- Cavuno does not send manual or bulk emails to the list.

## Dashboard-only tasks

Export the list (Job alerts → **...** → Export), import a CSV, resend a
confirmation email, and edit email templates (Email editor).

Sources: https://cavuno.com/docs/job-alerts,
https://cavuno.com/docs/job-alerts/manage-subscribers,
https://cavuno.com/docs/job-alerts/email-notifications,
https://cavuno.com/docs/job-alerts/email-deliverability,
https://cavuno.com/docs/billing/plans-and-pricing
