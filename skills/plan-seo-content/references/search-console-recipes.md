# Search Console reads for content planning

All reads go through `query` with path `/analytics/search-console/query`.
Cavuno supplies the property, the credential, `dataState: "final"` and the
board's hostname filter; never send a property or site URL. Required body
fields: `startDate` and `endDate` (`YYYY-MM-DD`). Optional: `dimensions`
(up to 3 of `query`, `page`, `date`, `country`, `device`,
`searchAppearance`), `type` (default web), `dimensionFilterGroups` (one
group, `groupType: "and"`, up to 20 filters), `rowLimit` (up to 25,000),
`startRow` (for paging).

## Pages the board earns clicks for

```json
{ "startDate": "2026-07-20", "endDate": "2026-10-16",
  "dimensions": ["page"], "rowLimit": 1000,
  "dimensionFilterGroups": [{ "groupType": "and", "filters": [
    { "dimension": "page", "operator": "contains", "expression": "/blog/" } ] }] }
```

Match each row's `keys[0]` (a full URL) to the inventory by slug.

## Queries the board already appears for

`dimensions: ["query"]`, same window, no page filter. Queries with high
impressions and a position outside the top results are the fastest
opportunities; queries with a board page already ranking are not blog
topics.

## Queries split across posts (cannibalisation)

```json
{ "startDate": "2026-07-20", "endDate": "2026-10-16",
  "dimensions": ["query", "page"], "rowLimit": 25000,
  "dimensionFilterGroups": [{ "groupType": "and", "filters": [
    { "dimension": "page", "operator": "contains", "expression": "/blog/" } ] }] }
```

Group rows by `keys[0]` (query). Keep queries where two or more pages each
have impressions worth acting on, and list each page's clicks,
impressions and position. If `rows` holds `rowLimit` rows, read the next
page with `startRow` set to the number already read.

## Read the response

- Rows: `{ keys, clicks, impressions, ctr, position }`; `keys` follow the
  order of `dimensions`.
- `cavuno.coverage.hostnames` lists the hosts the data covers.
- `cavuno.exhaustive` is always false: rows are top rows, so low-volume
  and anonymized queries are missing and rows won't sum to totals.
- `cavuno.latestObservedDate`, when present, is the latest date with
  data; end windows on or before it. Dates are in `America/Los_Angeles`.
- `ok: false` is an error (often Search Console not connected), not "no
  traffic". Say so and plan from the other evidence.

Sources: https://cavuno.com/docs/mcp/reference/search-console,
https://cavuno.com/docs/mcp/cookbook/research-keywords,
https://cavuno.com/blog/job-board-seo
