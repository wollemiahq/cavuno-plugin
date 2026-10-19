# KPI framework for a two-sided job board

## Contents

- The four questions
- Job seeker side
- Employer side
- Marketplace health
- Revenue
- Search
- Growth accounting for employers
- What the API cannot answer

## The four questions

Organize every review by business question, not by tool:

1. Are job seekers finding the board and acting? (demand)
2. Are employers posting and coming back? (supply)
3. Are the two sides creating value for each other? (marketplace)
4. Is that value turning into revenue?

For most boards employers are the harder side to win and keep, so a rise in
traffic with falling postings is a warning, not a win. Separate inputs the
operator controls (jobs posted, emails sent, content published) from
outputs (apply clicks, revenue, repeat posting); when an output moves,
trace which input changed.

## Job seeker side

| KPI | How to get it |
| --- | --- |
| Sessions, page views, average session duration | `/analytics/overview` |
| Apply clicks (core conversion) | `job_apply_clicks` in the overview; by job and channel from `/analytics/apply-clicks` |
| Click-to-apply rate per job | apply clicks ÷ job visitors (`/analytics/job-visitors`), same range |
| Source mix | `sources` in `/analytics/traffic`; channels in apply-click rows |
| Geography and device fit | `locations`, `devices` in `/analytics/traffic`; compare with where the jobs are |
| Job pages vs other pages | `pages` in `/analytics/traffic`, grouped by path pattern |
| Alert audience | `/subscribers/count` (all-time confirmed) and the `confirmed_subscribers` capacity in `/usage` |

## Employer side

| KPI | How to get it |
| --- | --- |
| Jobs posted in the period | `query` `/jobs/search` with `filters.publishedAt` for the period; count, following `nextCursor` |
| Active jobs | `/usage` capacity `active_jobs` (`used`, `limit`), or `/jobs/search` with `status: ["published"]` |
| Active employers | distinct `companyId` among jobs posted in the period |
| New vs returning employers | a company is returning if it also posted in an earlier period |
| Concentration | share of the period's jobs from the top company |
| Freshness | share of published jobs with `publishedAt` in the last 30 days |
| Paying employers | `/employer-subscriptions` by `status`; `customerLabel` on paid `/transactions` |

Keep backfilled or imported jobs in mind: they add inventory but are not
employer demand. Say whether counts include them when the user can tell.

## Marketplace health

- Apply clicks per published job in the period, compared with the previous
  period.
- Jobs with zero apply clicks in the period: list them for the operator
  (title and company) as candidates to promote or refresh.
- Cross-side movement: did sessions and jobs posted move in the same
  direction?

## Revenue

From `/transactions` (paid, per currency) and the overview's
`stripe_revenue`. Revenue per paid post = card post revenue ÷ count of
`card_post` transactions. Recurring revenue and churn come from the Stripe
Dashboard. See the set-up-monetization skill for the totals procedure.

## Search

Clicks, impressions, CTR and position by query and by page from Search
Console, segmented by page type (job pages, category pages, blog). Indexing
problems: inspect a few important URLs.

## Growth accounting for employers

Per period, using companies that posted:

- New: posted this period, never before.
- Retained: posted last period and this one.
- Churned: posted last period, not this one.
- Resurrected: posted this period after a gap of at least one period.

Net growth = new + resurrected − churned. Quick ratio = (new +
resurrected) ÷ churned; above 1 means additions exceed losses. When churned
is zero, report the counts instead of a ratio. There is no universal
healthy threshold; compare the board with itself over time. A job seeker
who stops visiting after finding a job is not a failure; employer churn
matters more.

These need jobs from earlier periods: run one `/jobs/search` per period and
keep only `companyId` and `publishedAt`.

## What the API cannot answer

- Per-job views and submitted applications (dashboard Jobs list).
- Which companies hold a membership level (dashboard Companies).
- Hires or fill rate (ask employers; report as unknown otherwise).
- Email opens and clicks for Cavuno digests (not tracked; Email channel
  visits from tagged links are the proxy).

Sources: https://cavuno.com/blog/job-board-analytics,
https://cavuno.com/blog/job-board-retention,
https://cavuno.com/docs/analytics/dashboard-analytics,
https://cavuno.com/docs/monetization/transactions-ledger,
https://cavuno.com/docs/job-alerts/email-deliverability
