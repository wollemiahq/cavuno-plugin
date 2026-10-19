---
name: manage-employers
description: Manages the companies and employer accounts on a Cavuno job board. Finds and edits company profiles, cleans up duplicate companies (there is no merge operation, so jobs are moved first), previews the jobs a delete would remove, blocks and unblocks companies, approves or rejects employer claims, deactivates or reactivates employer accounts, and drafts outreach emails to companies hiring in the board's niche for the user to send. Use when the user says "delete this company", "merge these two companies", "block Acme from the board", "approve the pending employer claims", "deactivate this employer's account" or "write outreach emails to companies hiring on my board".
---

# Manage employers

Follow the `cavuno-basics` skill for tool use, confirmations and read-back.

## When to use

- Company profiles: find, edit, markets, duplicates, delete, block.
- Employer people: claims, accounts, company memberships.
- Outreach emails to employers whose jobs the board already carries.

Not here: posting jobs for a company (`post-jobs`); stopping a careers-page
import (`source-jobs`); employer pricing, plans and coupons
(`set-up-monetization`); traffic reports (`review-performance`); bringing
companies over from another platform (`migrate-from-another-platform`).

## Steps: companies

1. **Find the record.** `query` `/companies/search` with `{ query }`
   (filter `markets` if needed), then `read` `/companies/{id}`. Note
   `jobCount`, `publishedJobCount` and `links.public`.
2. **Edit.** `update` PATCH `/companies/{id}` with only the changed fields
   (`name`, `website`, `summary`, `description`, social URLs, `markets`
   slugs from `/taxonomies/markets`). Changing `slug` changes the public
   URL (the old one redirects to it); say so before doing it.
3. **Duplicates.** There is no merge operation. Follow the procedure in
   [references/companies-and-blocklist.md](references/companies-and-blocklist.md):
   pick the survivor, move the duplicate's jobs with PATCH `companyId`,
   copy missing profile fields, then delete the empty duplicate.
4. **Delete with an impact preview.** `read` `/companies/{id}/jobs` (every
   status, all pages) and show the count by status and up to five titles.
   Delete removes the company and every one of those jobs permanently. Ask
   which outcome the user wants (table in the reference): delete, delete
   and block (`delete` `/companies/{id}` with `{ block: "true" }`), or a
   block. Then `read` `/companies/{id}` and expect 404.
5. **Block or unblock.** `create` `/companies/{id}/block` with
   `{ reason, remove }`. Ask which outcome: `remove: true` takes the profile
   and jobs off the board; omitted or `false` keeps the profile and
   archives published jobs. Check `removed`, `removal_scheduled` and
   `was_already_blocked` in the response. `create` `/companies/{id}/unblock`
   does not republish archived jobs.

## Steps: employer accounts

1. **Claims.** `read` `/employers/claims` (pending by default; `companyId`
   optional). For each claim, compare the `workEmail` domain and
   `workEmailVerifiedAt` with the company website. Show a table, flag
   mismatches and free email domains, and get a decision per claim. Then
   `create` `/employers/claims/{id}/approve` or `/reject`.
2. **Accounts and memberships.** `read` `/employers` (`search`),
   `/employers/{id}`, and `/employers/memberships` (`companyId`, `status`).
3. **Suspend access.** Prefer `create` `/employers/{id}/deactivate`: it
   blocks sign-in and keeps memberships, claims and jobs;
   `/employers/{id}/reactivate` restores sign-in. `delete` `/employers/{id}`
   permanently erases the person and their data, runs as an operation
   (202) to poll, and needs its own confirmation.
4. **Team changes** (remove a member, change a role, invite or revoke) are
   dashboard actions: Companies → row menu → Manage team.

## Steps: outreach emails

1. Pick prospects from board data with
   [references/outreach-emails.md](references/outreach-emails.md):
   companies with published jobs and no approved employer membership.
2. Gather real figures for a stated date range: `read`
   `/analytics/job-visitors` and `/analytics/apply-clicks` (`start`,
   `end`), `/subscribers/count`, `/analytics/overview`.
3. Draft each email in the chat, plus the next email in the sequence.
   Use only the figures from step 2; leave a bracketed placeholder for
   anything else (contact name, price, offer).
4. The user sends them. Never send email or create drafts in a mail tool
   unless the user asks for drafts there.

Done when:

- [ ] every delete, block and claim decision was confirmed after the user saw its impact;
- [ ] deleted companies read back as 404, edits read back with the new values;
- [ ] duplicate cleanup moved every job before the duplicate was deleted;
- [ ] outreach drafts contain only figures returned by Cavuno, with the date range, and nothing was sent.

## Guardrails

- A plain delete lets automated sourcing add the company back later;
  delete with `block=true` or block with `remove: true` keeps it off.
  Deleting a blocked company keeps it on the blocklist.
- The dashboard's block defaults to removing the company; the API's
  default (`remove` omitted) keeps the profile. Always send `remove`
  explicitly.
- Blocking matches the company ID, its normalized website domain and, for a
  removed company, its slug, not the name. A company with no website can
  be re-created under a different domain.
- Approving a claim lets that person post and see applicants for the
  company. Do not approve a claim the user has not seen.
- Contact details for outreach come from the user or the company's own
  published hiring or partnership addresses; do not collect personal emails.

## References

- [references/companies-and-blocklist.md](references/companies-and-blocklist.md): delete, block and keep outcomes, duplicate cleanup, what blocking stops.
- [references/employer-accounts.md](references/employer-accounts.md): claims checks, memberships, roles, deactivate versus delete.
- [references/outreach-emails.md](references/outreach-emails.md): prospect selection, figures to use, email templates and cadence.
