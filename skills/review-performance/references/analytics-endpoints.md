# Analytics endpoints

## Contents

- Shared rules
- Overview
- Traffic
- Apply clicks by job
- Job visitors
- Channel rules
- Caveats to state in reports

## Shared rules

- `start` and `end` are inclusive UTC dates (`YYYY-MM-DD`). Omit both for
  the default: the last 30 completed UTC days ending yesterday. Maximum
  range 366 days.
- Every call returns `{ status, ok, data }`. Check `ok`; a failed call is
  not an empty period.
- All four require `analytics.read`.

## Overview

`GET /analytics/overview` → `range`, `comparisonRange` (the previous period
of the same length), `currentVisitors` (sessions active in the last five
minutes; ignore for historical reviews) and `metrics`.

Metric keys: `sessions`, `page_views`, `average_session_duration`
(seconds), `job_apply_clicks`, `impressions` (Search Console),
`stripe_revenue`, `adsense_revenue` (currency, lowercase code in
`currency`). Conditional metrics are left out when unavailable. Each has
`current.total`, `current.change.absolute`, `current.change.percentage`
(null when both periods are zero), `current.points` and `previous`.
`dataAvailableThrough` marks the last complete day for lagging providers
(Search Console, AdSense).

## Traffic

`GET /analytics/traffic` with `start`, `end`, `limit` (1 to 10 rows per
table, default 10) and `campaignGroupBy` (`campaign` or `content`).

Tables: `pages` (`pathname`, `visits`, `visitsChangePercentage`),
`sources` (normalized referrer, `visits`, `applyClicks`), `locations`,
`devices`, `campaigns` (`campaign`, `source`, `medium`, and with
`content`: `content`, `term`; plus `visits`, `applyClicks`). Campaigns are
first-touch, tagged sessions only.

## Apply clicks by job

`GET /analytics/apply-clicks` with `start`, `end`, `limit` (1 to 1000,
default 100), `cursor`. Rows: `jobId`, `companySlug`, `jobSlug`, `channel`,
`applyClicks`, one per job and first-touch channel. Sum rows per `jobId` for
a job's total. Join to jobs on `jobId` = job `id`.

These are clicks on Apply, not submitted applications, and attribution is
first-touch, not last-click. In-app browsers such as LinkedIn's often show
as `direct`.

## Job visitors

`GET /analytics/job-visitors`, same parameters. Rows: `companySlug`,
`jobSlug`, `channel`, `campaign`, `utmSource`, `utmMedium`, `visitors`
(unique sessions). Join on `companySlug` + `jobSlug` (not `id`); a renamed
slug splits history. Visitors are lower than page views by design.

Click-to-apply rate for a job = its apply clicks ÷ its visitors over the
same range. Label it as computed from sessions.

## Channel rules

Each session gets one channel from its first page view:

1. If the page has `utm_medium`: `cpc`, `ppc` or `paid` → Paid search
   (Paid social when the source is a social site); `email` → Email;
   `social` → Organic social.
2. Otherwise by referrer: search engines → Organic search, social sites →
   Organic social, known AI chat domains → A.I., other sites → Referral.
3. No referrer → Direct.

## Caveats to state in reports

- Counts are tracked sessions, not people; one person on two devices
  counts twice.
- When Require cookie consent is on, visitors who do not accept are not
  counted.
- Automated traffic is filtered from visits and page views (not from apply
  clicks), and today's numbers can drop slightly as filtering runs.
- There is no exit-page report.
- Per-job views and applications are in the dashboard Jobs list; the
  dashboard cannot export them, and the jobs export is dashboard-only.

Sources: https://cavuno.com/docs/analytics/dashboard-analytics,
https://cavuno.com/docs/analytics/cavuno-analytics-data,
https://cavuno.com/docs/analytics
