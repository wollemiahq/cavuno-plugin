---
name: review-performance
description: Reviews how a Cavuno job board is performing for job seekers and employers, using built-in analytics (sessions, page views, traffic sources, UTM campaigns, job visitors, apply clicks), Google Search Console queries and URL inspection, job inventory and revenue, and writes the result as an operator summary, an employer results report or a stakeholder or member-benefit report for an association. Read-only. Use when the user says "how did the board do last month?", "which channels drive apply clicks?", "what Google searches bring people to us?", "did our newsletter campaign work?", "is this job page indexed?", "write a report for our board meeting" or runs /cavuno:performance.
---

# Review performance

Follow the `cavuno-basics` skill for tool use, confirmations and read-back.

## When to use / not

Use for any question about traffic, apply clicks, search visibility, job
inventory trends or revenue over time, and for written reports to
stakeholders. `/cavuno:performance` runs this skill for the last 30 days.

Not for: changing prices or plans (`set-up-monetization`), drafting
promotion (`promote-jobs`), or fixing settings. This skill makes no writes.

## Steps

1. **Fix the question, period and audience.** Default period: the last 30
   completed days, the API's own default, compared with the 30 days before.
   Audience: the operator, an employer, or a board or committee. Ask only
   when the audience changes the report.
2. **Job seeker side.** `read` `/analytics/overview` with `start` and `end`
   (UTC `YYYY-MM-DD`, at most 366 days). It returns `range`,
   `comparisonRange` and `metrics` with `current.total`,
   `current.change.percentage` and `previous.total`. A metric missing from
   the list is unavailable, not zero.
3. **Where visits come from.** `read` `/analytics/traffic` (same dates,
   `limit` up to 10 per table) for pages, sources, locations, devices and
   campaigns; add `campaignGroupBy: content` to compare posts within one
   campaign.
4. **Which jobs perform.** `read` `/analytics/apply-clicks` and
   `/analytics/job-visitors` (`limit` up to 1000, follow `nextCursor`).
   Join apply clicks on `jobId`, visitors on `companySlug` + `jobSlug`; see
   [references/analytics-endpoints.md](references/analytics-endpoints.md).
5. **Employer side.** `query` `/jobs/search` with
   `filters.publishedAt` for jobs posted in each period and group by
   `companyId` (new and returning employers); `read` `/usage` for active
   jobs against the plan; `read` `/transactions` and
   `/employer-subscriptions` for revenue as in
   [references/kpi-framework.md](references/kpi-framework.md).
6. **Search.** `query` `/analytics/search-console/query` with `startDate`,
   `endDate`, `dimensions` (`query`, `page`, `country`, `device`, `date`)
   and `rowLimit`. Check `ok` on every call; a 409, 429, 502 or 503 means
   no data was returned, not zero searches. To check one page, `query`
   `/analytics/search-console/inspect` with `inspectionUrl`. Details:
   [references/search-console.md](references/search-console.md).
7. **Interpret** both sides together with the KPI framework: what moved,
   on which side, the likely input that changed (jobs posted, a campaign,
   a new employer), and what to watch next. Present causes as hypotheses.
8. **Write the report** in the shape for the audience from
   [references/stakeholder-reports.md](references/stakeholder-reports.md),
   with dates, sources and caveats.

Done when:

- [ ] The exact date ranges are stated, with UTC for Cavuno analytics and America/Los_Angeles for Search Console.
- [ ] Both job seeker and employer measures are covered, or the reply says which side the user excluded.
- [ ] Every number comes from a tool response in this conversation, with failed calls reported by error code.
- [ ] Apply clicks are not called applications, and sessions are not called people.
- [ ] No benchmark appears unless its source is named.

## Guardrails

- Read-only: no `create`, `update` or `delete` calls. If the review
  suggests a change, describe it and name the skill that makes it.
- Tracker limits: ad blockers, privacy browsers and declined cookie
  consent stop tracking, so visits and apply clicks are undercounts;
  applications are counted server-side and can exceed apply clicks.
- Search Console rows are top rows, not exhaustive; query rows do not add
  up to the impressions total. Do not sum them as if they were.
- Do not compare Cavuno sessions with GA4 users as if they measured the
  same thing.
- Small numbers: when a period has few events, give counts, not
  percentages, and say the change may be noise.
- No personal data: report employers by company name, never list candidate
  or subscriber emails, and do not pull exports (dashboard only).
- Per-job views and applications counts are in the dashboard Jobs list,
  not the API; say so when the user asks for them.

## References

- [references/analytics-endpoints.md](references/analytics-endpoints.md): parameters, fields, channel rules, joins and caveats for `/analytics/*`.
- [references/search-console.md](references/search-console.md): query and inspect bodies, coverage, dates and error handling.
- [references/kpi-framework.md](references/kpi-framework.md): two-sided marketplace KPIs and how to compute each from the API.
- [references/stakeholder-reports.md](references/stakeholder-reports.md): operator, employer and association report templates.
- [examples/monthly-review.md](examples/monthly-review.md): a monthly review from calls to summary.
