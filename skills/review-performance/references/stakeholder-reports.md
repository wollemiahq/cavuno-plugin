# Report templates

Every report states the date range (UTC for Cavuno analytics,
America/Los_Angeles for Search Console), the data sources, and a short
"how to read these numbers" note. Numbers come from tool results; anything
unknown is written as "not available" with the reason.

## Operator summary (weekly or monthly)

1. Headline: three bullets, each a change with its cause as a hypothesis
   ("Apply clicks up 18% (412 vs 349); likely the two featured roles from
   Volta").
2. Job seekers: sessions, apply clicks, top sources and campaigns.
3. Employers: jobs posted, active employers, new vs returning, top posters.
4. Revenue: paid per currency, open invoices.
5. Search: top queries and pages, any indexing problem found.
6. Watch next: two or three specific actions, each with the skill that
   does it (for example, promote zero-click jobs with `promote-jobs`).

Weekly reviews focus on traffic, campaigns and jobs posted; monthly
reviews add employer retention, revenue and search.

## Employer results report

For one company's jobs, to support renewals.

- Their jobs in the range (from `/jobs/search` with `companyId`), each with
  job-detail visitors and apply clicks, plus the channels that sent them.
- Lead with apply clicks from the right audience, not impressions or page
  views.
- Say what apply clicks are (clicks on Apply, not confirmed applications)
  and that tracking undercounts visitors who block it.
- Do not include other companies' figures or candidate details.

## Association stakeholder or member-benefit report

For a board of directors, a committee or the membership, one page.

1. **Purpose line:** the career center's role for members (career
   development is a leading reason people join associations, per the
   member-retention post).
2. **Value to members:** jobs available in the period, jobs posted, apply
   clicks, job alert audience (say the count is all-time confirmed
   sign-ups), top employers hiring members.
3. **Value to employers:** number of employers posting, returning
   employers.
4. **Non-dues revenue:** paid revenue per currency for the period, by
   product kind; open invoices.
5. **Reach and visibility:** sessions, top sources, search clicks.
6. **Next quarter:** two or three proposals tied to member engagement (for
   example, more alert signups, employer outreach to member companies).
7. **Notes:** definitions and limits.

Member counts, membership renewals and which member companies posted are
not in the Cavuno API; ask the user for them or mark them "not available".
Present outside figures (such as an association's job board revenue in a
blog case study) only when the user asks for context, with the source.

## Writing rules

- Absolute numbers next to every percentage; percentages only when the
  base is large enough to mean something.
- Same range and comparison period throughout one report.
- Plain language: "people who clicked Apply", not "conversion events".
- Offer to save the report as a document or page if the user will share it.

Sources: https://cavuno.com/blog/job-board-analytics,
https://cavuno.com/blog/member-retention-strategies,
https://cavuno.com/blog/non-dues-revenue,
https://cavuno.com/docs/analytics/dashboard-analytics
