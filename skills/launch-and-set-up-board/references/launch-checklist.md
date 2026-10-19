# Launch checklist

## Contents

- How to use this list
- Before launch
- Launch day
- First week and first month
- Association and member boards
- Niche checks before building

## How to use this list

For each item, mark it **done** (a read proves it), **missing** (a read
shows it is not done) or **check in the dashboard** (the connector cannot
see it). Do not mark an item done from the user's say-so when a read can
confirm it.

New boards start on a 14-day Starter trial behind a password, so generated
content is not indexed before review. Publishing is included in the trial.
If the trial ends unsubscribed, a published board goes back behind its
password; content is kept.

## Before launch

| Item | How to check | Track |
| --- | --- | --- |
| Board name and contact details set | `/settings`: `name`, `config.contactEmail`, `config.companyLegalName` | Settings |
| Niche and audience written down | Ask the user; it drives generated content and backfill rules | Conversation |
| Custom domain active and primary (optional, recommended) | `/domains`: a row with an active `status` and `isPrimary: true` | Domain |
| Job categories match the niche | `/taxonomies/categories/tree` | `organize-taxonomy-and-fields` |
| Job form asks for what the niche needs | `/settings/job-form`, `/settings/form-layouts` | Job form |
| Approval rules chosen | `/settings`: `requireApprovalFreeJobs`, `requireApprovalAggregatedJobs` | Settings |
| Job alerts on | `/settings`: `config.jobAlertsEnabled` | Settings |
| Default job duration chosen | `/settings`: `config.defaultJobDurationDays` (30 days when unset) | Settings |
| Old site's URLs redirected (migrations) | `/redirects` | Redirects |
| Payments set up, if charging employers | Dashboard: Settings → Products | `set-up-monetization` |
| Branding, pages, legal pages, footer | Dashboard: Website builder | Dashboard |
| Branded sender email | Dashboard: Settings → Email domain | Dashboard |
| Google Search Console and analytics connected | Dashboard: Settings → Google Search Console, Settings → Analytics | Dashboard |

## Launch day

| Item | How to check |
| --- | --- |
| 20 to 50 relevant jobs live | `/jobs?status=published` count; below that, seed first |
| No placeholder text, every job has a working apply path | Sample `GET /jobs/{id}`: `applicationUrl`, `description` |
| Domain serves HTTPS | Domain active; SSL provisioning can take up to 15 minutes after verification |
| Password protection off | `/settings`: `config.passwordProtectionEnabled` is `false` |
| Builder site published | Dashboard: turning protection off does not publish an unpublished builder version |
| Signed-out test | User opens the board in a private window, opens a job, follows its apply path, subscribes to an alert |

Turning protection off is the launch. Leaving it on blocks visitors and
search engines.

## First week and first month

- Watch for errors and inquiries; fix quickly.
- Check that jobs keep arriving (`source-jobs`) and that the pending queue
  is reviewed (`tidy-board`).
- Turn on the Google Indexing API once a primary custom domain is active and
  the board is public (`tidy-board`).
- Start employer outreach and regular content (`promote-jobs`,
  `plan-seo-content`).

A soft launch to a small group of contacts and early employers is a good
way to test posting, applying and alerts before a full announcement.

## Association and member boards

Add these for an association, society or member organization:

- Written primary goal and baseline measures, and named owners for program,
  employers, membership, moderation, marketing, finance and DNS.
- Member and public posting rules written plainly. There is no membership
  system sync, so someone owns membership assignment and expiry.
- `postingRequiresMembership` decided: when true, only companies with an
  active plan assignment can post.
- Checkout tested as a public employer and as a member employer.
- Refund, moderation, privacy and support policies linked from the board.
- A review date and decision rule on the calendar. If a board of directors
  must approve the program, frame it as a bounded pilot with a transparent
  budget, staff capacity and a written motion.

## Niche checks before building

If the user is still choosing a niche, test it before setup: search demand
for niche job terms, how fragmented the employers are, how many relevant
openings appear each month, whether employers will pay, and whether a
community already gathers around careers in the niche. A two-week sprint
(research and 30 employer messages in week one, calls and a go/no-go score
in week two) is enough to decide. Start with the smallest niche the board
can lead; aggregated jobs give volume, exclusive listings give the board its
reason to exist.

Sources: https://cavuno.com/docs/getting-started/launch-checklist,
https://cavuno.com/docs/getting-started/quick-start,
https://cavuno.com/docs/getting-started/getting-first-jobs,
https://cavuno.com/docs/board-setup/password-protection,
https://cavuno.com/docs/troubleshooting,
https://cavuno.com/blog/create-a-job-board,
https://cavuno.com/blog/association-job-board,
https://cavuno.com/blog/board-approval-association-job-board,
https://cavuno.com/blog/validate-job-board-niche,
https://cavuno.com/blog/job-board-ideas,
https://cavuno.com/blog/job-board-mistakes
