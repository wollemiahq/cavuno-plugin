# Health checks

One row per check: what to read, what is healthy, and what to flag. "Fix
with" names the skill or dashboard page that handles the problem.

## Sign-in and role

| Field (`/me`) | Healthy | Flag |
| --- | --- | --- |
| `board.name`, `board.slug` | The board the user means | A different board: the credential is bound to one board, so reconnect and approve access for the right one |
| `actor.type` | `oauth_token` in Claude | `api_key` in a chat client is unusual; say which key-based setup is in use |
| `actor.role` | `owner` or `admin` for setup work | `member` can manage jobs, companies and the blog but not settings, domains, integrations, billing or the team |
| `actor.permissions` | Includes what the task needs | Missing permission for the user's next task |

The dashboard calls the `member` role "Editor". Only the primary owner can
transfer ownership or delete the board.

## Board access

| Signal | Flag when | Why it matters | Fix with |
| --- | --- | --- | --- |
| `config.passwordProtectionEnabled` | `true` on a board the user calls live | Visitors see a password page, search engines cannot crawl it, and Google Indexing API and IndexNow notifications are skipped | `launch-and-set-up-board` |
| `config.registrationWallEnabled` | `true` and the user is surprised | Visitors must sign in to view jobs | `launch-and-set-up-board` |
| Billing `status`, `active` | `active: false`, or a trial | A board on an unpaid trial goes back behind its password when the 14-day trial ends without a subscription | Dashboard: Settings → Billing |
| `cancelAtPeriodEnd` | `true` | The plan ends on `currentPeriodEnd` | Dashboard: Settings → Billing |

## Domains

| Signal (`/domains`) | Flag when | Fix with |
| --- | --- | --- |
| No row with `isPrimary: true` | The board has a custom domain but none is primary | `launch-and-set-up-board` |
| A row whose `status` is not active | Name the domain, quote `status` and `statusReason`, and give `createdAt` | `launch-and-set-up-board` (DNS walkthrough) |
| `primaryDomainId` is `null` | Only the Cavuno subdomain is in use | Optional; the Google Indexing API needs a primary custom domain |

DNS can take up to 48 hours to propagate. A domain pending for less than
that is not yet a problem; say when it was added.

## Usage

| Signal (`/usage` `capacities[]`) | Flag when | What to say |
| --- | --- | --- |
| `active_jobs` | `remaining` is 0, or smaller than the work the user plans | Publishing and approvals stop at the limit; backfill imports pause until slots free up. Employers can still post through the public form. Expire stale jobs (`tidy-board`) or upgrade in the dashboard |
| `confirmed_subscribers` | `remaining` is 0 | The plan's subscriber capacity is used up; compare plans in Settings → Billing |
| `team_seats` | `remaining` is 0 | No new invitations until a seat frees up |
| `builderCredits` | `remainingTenths` is 0 | Values are in tenths of a credit; divide by 10 |

`limit: null` and `remaining: null` mean unlimited. Never call that "0 left".

## Waiting on the operator

| Signal | Flag when | Note | Fix with |
| --- | --- | --- | --- |
| Jobs with `status: pending_approval` | Any | An aggregated job still waiting 30 days after it arrived is removed automatically; free submissions wait until someone acts | `tidy-board` |
| `/employers/claims` (pending) | Any | People asking to manage a company profile | `manage-employers` |

Approval publishes the job and counts against `active_jobs`. If pending jobs
outnumber `remaining`, say so.

## Indexing

| Signal (`/integrations/indexing`) | Flag when |
| --- | --- |
| `enabled: false` with an active primary custom domain and no password | Google is not being notified of job changes; the site is still indexed through normal crawling |
| `provisioningStatus: "error"` | Setup failed; retry from Settings → Indexing |
| `lastError` not null | Quote it |
| `indexNowEnabled: false` | Bing and other IndexNow engines are not notified |

Fix with `tidy-board`, which owns the indexing toggles.

## What "healthy" looks like

Signed in as owner or admin on the intended board, password protection off
(unless the user is still building), one active primary domain, every
capacity with room for planned work, pending queues near zero, and no
indexing error.

Sources: https://cavuno.com/docs/mcp/reference/authentication-and-security,
https://cavuno.com/docs/mcp/reference/usage-and-limits,
https://cavuno.com/docs/troubleshooting,
https://cavuno.com/docs/team/manage-roles,
https://cavuno.com/docs/managing-jobs/job-moderation,
https://cavuno.com/docs/board-setup/password-protection,
https://cavuno.com/docs/getting-started/quick-start,
https://cavuno.com/docs/job-board-seo/indexing-api,
https://cavuno.com/docs/job-board-seo/indexnow
