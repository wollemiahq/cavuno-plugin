# Employer accounts, claims and memberships

## Contents

- How employers join a company
- Reviewing claims
- Deactivate, reactivate or delete
- What only the dashboard can do

## How employers join a company

An employer is a board user with role `employer`. They act for a company
only through an approved membership. Memberships become approved by:

1. **Email domain match**: a verified work email on the company's website
   domain is approved automatically.
2. **Work-email verification**: verifying an address on the company domain
   approves the claim.
3. **Operator approval**: a claim that cannot be verified stays `pending`
   until the operator approves or rejects it.
4. **Invite**: a company admin or the operator invites an email address;
   invites expire after 7 days.

Roles are `admin` and `member`. Both manage the profile, jobs and
applicants; only an admin manages the team and can delete the company
(when "Employer company deletion" is on). A company with approved members
always has at least one admin. Employer accounts require the "Employer
accounts" feature (Settings → Features).

## Reviewing claims

`read` `/employers/claims` returns pending claims by default (`status`:
`pending`, `approved`, `rejected`; optional `companyId`). Each has
`companyName`, `employerEmail`, `workEmail`, `workEmailVerifiedAt`,
`requestedAt`.

| Check | Signal |
| --- | --- |
| `workEmail` domain equals the company website domain | Strong; verification would usually have approved it already. |
| Free email provider (gmail.com, outlook.com and similar) | Weak; ask the user how they know this person. |
| Domain of a different company, or a lookalike domain | Reject unless the user knows the relationship (for example an agency). |
| Several claims on one company in a short time | Ask the user before approving any. |
| `workEmailVerifiedAt` is null | The work address is unproven. |

Approve: `create` `/employers/claims/{id}/approve`. Reject: `create`
`/employers/claims/{id}/reject`. Both are idempotent; 409 means the claim
was already resolved the other way. Approving gives the person access to
the company's jobs and applicants.

`read` `/employers/memberships` (filters `status`, `companyId`) lists
approved, pending and rejected memberships with `approvalSource`.

## Deactivate, reactivate or delete

| Action | Call | Effect | Reversible |
| --- | --- | --- | --- |
| Deactivate | `create` `/employers/{id}/deactivate` | Sign-in disabled, all sessions revoked; memberships, claims and jobs kept. | Yes |
| Reactivate | `create` `/employers/{id}/reactivate` | Sign-in restored; the employer signs in again. | Yes |
| Delete | `delete` `/employers/{id}` | Permanently erases the person and their data, including memberships and claims. Returns 202 and an `employers.remove` operation; poll `/operations/{id}`. The result counts deleted resumes, saved jobs, alerts, sessions and memberships. | No |

Delete is for an erasure request. It does not delete the companies the
person managed. A later sign-up with the same verified work-email domain
can be auto-approved onto the company again; to prevent that, block the
company or change who can join in the dashboard.

## What only the dashboard can do

Companies → row menu → Manage team (or the company's edit drawer, Team
section):

- approve or deny requested access in place;
- make a member admin or member, remove a member;
- send, resend or revoke invites.

Removing the last member of a falsely claimed company leaves it unclaimed.
Settings → Features → "Employer company deletion" controls whether company
admins can delete their own company (on by default).

Sources: https://cavuno.com/docs/managing-companies/employer-team-access,
https://cavuno.com/docs/managing-companies/employer-company-deletion,
https://cavuno.com/docs/api
