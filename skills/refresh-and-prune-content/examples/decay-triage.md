# Example: a decay report

## Input

"Which of my blog posts are losing traffic?"

## What Claude did

- Listed 12 published posts and built URLs on `jobs.climatecareers.example`.
- Pulled `["date", "page"]` rows for `/blog/` pages; end date
  2026-10-16 (`latestObservedDate`), recent window 2026-09-19 to
  2026-10-16.

## Output (abridged)

| Post | Best earlier window | Clicks then / now | Lost clicks | Position then / now | CTR then / now | Diagnosis | Proposed action |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Solar installer salary | 2026-03-14 to 2026-04-10 | 1,240 / 410 | 830 | 3.1 / 7.8 | 5.9% / 2.6% | Ranking loss; ranges quote 2025 jobs | Data update from current jobs, then refresh |
| Green resume tips | 2026-02-01 to 2026-02-28 | 380 / 150 | 230 | 4.2 / 4.4 | 3.9% / 1.5% | Click-through loss: impressions held, position held | New `seoTitle` and `seoDescription` first |
| Heat pump technician guide | 2026-01-04 to 2026-01-31 | 620 / 540 | 80 | 5.0 / 5.2 | steady | Same dip last October | Keep and watch |

Excluded:

- "Climate jobs report 2026" (published 2026-08-25, under 90 days): still
  ramping, not ranked, even though its clicks fell further.
- "EV charging careers": best window 12 clicks, below the 50-click
  threshold used here.

"Which should I start with? I suggest the salary post: it lost the most
clicks and its fix is mostly fresh job data."

Sources: https://cavuno.com/docs/mcp/cookbook/refresh-blog-posts-with-search-console
