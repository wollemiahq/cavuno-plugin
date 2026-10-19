# Decay analysis with Search Console

## Contents

- Request bodies
- Window math
- Filters
- Diagnosis table
- Refresh brief

## Request bodies

All through `query`, path `/analytics/search-console/query`. Cavuno adds
the property, credential, `dataState: "final"` and the board's hostname
filter; send none of them.

Daily clicks per blog page (step 2):

```json
{ "startDate": "2024-11-01", "endDate": "2026-10-16",
  "dimensions": ["date", "page"], "rowLimit": 25000, "startRow": 0,
  "dimensionFilterGroups": [{ "groupType": "and", "filters": [
    { "dimension": "page", "operator": "contains", "expression": "/blog/" } ] }] }
```

If `rows` has 25,000 entries, repeat with `startRow: 25000`, and so on.
For a large blog, a lighter option is one `dimensions: ["page"]` query per
28-day window.

Queries for one post in one window (step 5):

```json
{ "startDate": "2026-09-19", "endDate": "2026-10-16",
  "dimensions": ["query"], "rowLimit": 1000,
  "dimensionFilterGroups": [{ "groupType": "and", "filters": [
    { "dimension": "page", "operator": "equals",
      "expression": "https://jobs.example.com/blog/solar-installer-salary" } ] }] }
```

Run it for the recent window and the best window, then compare.

Indexing check: `query` `/analytics/search-console/inspect` with
`{ "inspectionUrl": "https://jobs.example.com/blog/<slug>" }`.

## Window math

- End date: `cavuno.latestObservedDate` when present; otherwise the
  latest `date` in the rows.
- Recent window: the 28 days ending on the end date.
- Best earlier window: the 28-day run with the most clicks that ends
  before the recent window starts.
- Lost clicks = best window clicks minus recent clicks. Rank on this.
  The refresh cookbook: a page falling from two clicks to zero is not a
  better opportunity than one that lost substantial qualified traffic.
- Show impressions, CTR and average position for both windows too.

## Filters

| Exclude | Why |
| --- | --- |
| `publishedAt` within 90 days of the end date | Still inside its ramp; no stable peak yet |
| Posts substantially refreshed within 90 days (ask the user) | Same; `updatedAt` changes on any edit, so it can't tell you |
| Best window below a threshold you state | Not enough demand to justify the work |

List excluded posts with the reason; don't silently drop them.

## Diagnosis table

| Pattern | Likely cause | Action |
| --- | --- | --- |
| The same dip in the same weeks last year | Seasonality | Keep; re-check after the season |
| Impressions down, position steady, across most queries | Falling search demand | Keep, or retarget to queries still growing |
| Most blog posts (or the whole board) drop in the same week | Sitewide change: an algorithm update, a site or domain change | Investigate sitewide before touching one post |
| Clicks and impressions fall to near zero suddenly | Tracking, indexing or a removed page | URL Inspection; check the post is published |
| Impressions steady or up, position steady, CTR down | Click-through loss: a weaker title, a new SERP feature, rivals' snippets | Metadata fix: `seoTitle`, `seoDescription` |
| Position worse on the main queries | Ranking loss: content decay, stronger competitors | Data update, then refresh |
| New queries appear while old ones fade; results now show a different page type | Intent shift | Refresh toward the new intent, or leave it if a board page fits better |
| Another post now gains the queries this one lost | Cannibalisation | Merge or differentiate |

The SEO guide's refresh triggers: decline for three or more consecutive
months, outdated statistics or screenshots, better competing content, a
shift in search intent.

## Refresh brief

Record before drafting: peak and recent windows; lost, declining, holding
and emerging queries; recent impressions, CTR and position; the diagnosis;
what to keep (sections, images, links, the queries still working); what
the strongest current results add that the post lacks; and the concrete
information gain (fresh job data, a decision framework, a current
example). "Make it longer" is not a brief.

Sources: https://cavuno.com/docs/mcp/cookbook/refresh-blog-posts-with-search-console,
https://cavuno.com/docs/mcp/reference/search-console,
https://cavuno.com/docs/mcp/reference/blog-posts,
https://cavuno.com/blog/job-board-seo
