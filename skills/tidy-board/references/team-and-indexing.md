# Team and indexing

## Contents

- Roles
- Review the team
- Member and invitation actions
- Google Indexing API
- IndexNow

## Roles

The API's roles are `owner`, `admin` and `member`. The dashboard calls
`member` "Editor".

| Role | Can |
| --- | --- |
| `owner` | Everything, including deleting the board; the primary owner alone can transfer ownership |
| `admin` | Everything except deleting the board and transferring ownership: settings, domains, integrations, API keys, billing, the team |
| `member` (Editor) | Jobs including publishing, companies, employers, job seekers (not erasing), the blog, the inbox; view-only for job alerts, the job form, collections and email templates |

Only an owner can promote someone to owner or change another admin's role.
Give the least role that does the job.

## Review the team

1. `read` `/members` (filters: `role`, `suspended`). Each member has
   `userId`, `name`, `email`, `role`, `suspended`, `joinedAt`,
   `isPrimaryOwner`.
2. `read` `/invitations` with `status: "pending"` and `status: "expired"`.
3. `read` `/usage` for `team_seats`.
4. Report counts by role, suspended members, pending and expired invites,
   and seats left. Ask the user who has left or changed jobs; do not guess
   from join dates.

## Member and invitation actions

| Goal | Call | Notes |
| --- | --- | --- |
| Invite | `POST /invitations` `{ email, role }` | `role` is `admin` or `member`; one email per call; sends the email |
| Change a pending invite's role | `PATCH /invitations/{id}` `{ role }` | |
| Resend an invite | `POST /invitations/{id}/renew` | New link, expiry extended by 7 days |
| Cancel an invite | `DELETE /invitations/{id}` | Cancel expired or wrong invites rather than leaving two |
| Change a role | `PATCH /members/{userId}` `{ role }` | Primary owner's role cannot change here |
| Pause access | `POST /members/{userId}/suspend` | Keeps the role, removes all permissions; `unsuspend` restores |
| Remove | `DELETE /members/{userId}` | Access ends at once; their work stays. The owner cannot be removed |
| Transfer primary ownership | `POST /members/transfer-ownership` | Primary owner only; the old owner becomes admin |

Invite and renew responses include an accept `token` and `acceptUrl`. Do not
print them; the invitee gets the email. Suspend is the reversible choice
when the user is unsure.

## Google Indexing API

`GET /integrations/indexing` returns `enabled`, `indexNowEnabled`,
`provisioningStatus` (`pending`, `provisioning`, `ready`, `error`, or
`null`), `lastRunAt` and `lastError`.

The site is indexed by Google either way; the API only tells Google sooner
when jobs are published, edited or removed.

Turn on with `POST /integrations/indexing/toggle-google-indexing-api`
`{ "enabled": true }`. It checks, in order:

| 409 code | Meaning | Fix |
| --- | --- | --- |
| `integrations_indexing_custom_domain_required` | No primary custom domain | Connect one and make it primary (`launch-and-set-up-board`) |
| `integrations_indexing_password_protected` | Board is password protected | Remove protection first |
| `integrations_indexing_setup_not_ready` | Google Cloud project not provisioned; `details.provisioningStatus` says where it is | Start or retry setup in Settings → Indexing |
| `integrations_indexing_search_console_access_required` | The service account is not an Owner in Search Console | Add `details.serviceAccountEmail` as an Owner of the property, then retry |

A 502 `integrations_indexing_verification_failed` means Google did not
confirm access; retry later. Connecting Search Console in the dashboard adds
the service account automatically. Disabling always succeeds.

Google's default quota is 200 publish requests per project per day, shared
by updates and removals. Submission history is in Settings → Indexing →
View submissions.

## IndexNow

Notifies Bing, Yandex and other participating engines; Google does not take
part. New boards have it on. Toggle with `POST
/integrations/indexing/toggle-indexnow` `{ "enabled": true }`. Enabling is
refused (409) while password protection is on. The key file is served at
`/indexnow-key.txt` on the board; if it returns 404, turning IndexNow off
and on again creates the key.

Both are skipped while the board is password protected or offline.

Sources: https://cavuno.com/docs/team/invite-team-members,
https://cavuno.com/docs/team/manage-roles,
https://cavuno.com/docs/job-board-seo/indexing-api,
https://cavuno.com/docs/job-board-seo/indexnow,
https://cavuno.com/blog/google-indexing-api-job-postings,
https://cavuno.com/blog/indexnow-for-job-boards
