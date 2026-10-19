# Example: monthly review

## Input

"How did the board do last month?" (asked on 2026-10-19)

## Plan

1. `read` `/analytics/overview` with `start: 2026-09-01`, `end: 2026-09-30`
   → sessions 6,210 (previous 5,480), apply clicks 412 (previous 349),
   `stripe_revenue` USD 1,890.00. `comparisonRange` 2026-08-02 to
   2026-08-31.
2. `read` `/analytics/traffic`, same dates → top sources google 2,900,
   linkedin 610; campaign `weekly-jobs-2026-09-15` 180 visits, 22 apply
   clicks.
3. `query` `/jobs/search` with `filters.publishedAt` for September and for
   August → 34 jobs from 19 companies (August: 29 from 18); 6 companies did not post in August.
4. `read` `/transactions` (page until rows are older than 2026-09-01) →
   paid USD 1,890.00 over 9 transactions; 1 open invoice.
5. `query` `/analytics/search-console/query` with `startDate` 2026-09-01,
   `endDate` 2026-09-30, `dimensions: ["query"]`, `rowLimit: 10` → `ok`
   true, top query "climate policy jobs".

## Output

**September 2026 (UTC) vs August 2–31**

- Job seekers: 6,210 sessions (+13%), 412 apply clicks (+18%). Google sent
  the most visits; the 15 September newsletter drove 22 apply clicks.
- Employers: 34 jobs posted (+5), 19 companies posting, 6 that did not post in August.
- Revenue: USD 1,890.00 paid from 9 transactions; one invoice still open.
- Search (America/Los_Angeles dates, finalized data): top query "climate
  policy jobs".

How to read this: sessions are tracked visits, not people; apply clicks are
clicks on Apply, not submitted applications; visitors who block tracking
are not counted.

Watch next: 7 published jobs had no apply clicks; I can draft posts for
them with the promote-jobs skill.
