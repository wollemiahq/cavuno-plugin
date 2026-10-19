# Google Search Console through Cavuno

Both calls go through the `query` tool and need `analytics.read`. Cavuno
supplies the connected property and credential; never ask the user for
them.

## Performance query

`POST /analytics/search-console/query` body:

| Field | Values |
| --- | --- |
| `startDate`, `endDate` (required) | `YYYY-MM-DD`, in America/Los_Angeles |
| `dimensions` | up to 3 of `query`, `page`, `country`, `device`, `date`, `searchAppearance` |
| `type` | `web`, `image`, `video`, `news`, `discover`, `googleNews` |
| `dimensionFilterGroups` | at most one group: `{ groupType: "and", filters: [{ dimension, operator, expression }] }`; operators `contains`, `equals`, `notContains`, `notEquals`, `includingRegex`, `excludingRegex` |
| `aggregationType` | `auto`, `byPage`, `byProperty` |
| `rowLimit` | 1 to 25000 |
| `startRow` | 0 to 250000, for paging |

The response has `rows` (`keys`, `clicks`, `impressions`, `ctr`,
`position`) and `cavuno`: `coverage` (`mode` `board_hosts` or
`whole_property`, `hostnames`), `dataState` `final`, `dataTimezone`
`America/Los_Angeles`, `exhaustive: false`, and sometimes
`latestObservedDate`.

Recipes:

- Top queries: `dimensions: ["query"]`, `rowLimit: 25`.
- Pages with impressions but few clicks: `dimensions: ["page"]`, then sort
  by impressions and look at low `ctr`.
- Job pages only: filter `page` `contains` `/jobs/`. Segment blog, job and
  category pages separately; their click rates differ.
- Trend: `dimensions: ["date"]`.

## URL inspection

`POST /analytics/search-console/inspect` with `inspectionUrl` (a canonical
HTTPS board URL) and optional `languageCode`. Returns Google's indexed copy:
`indexStatusResult` (`verdict`, `coverageState`, `lastCrawlTime`,
`googleCanonical`, `userCanonical`, `sitemap`, ...). It does not run a live
test or request indexing. Quotas apply: inspect only the URLs the task
needs.

## Errors are not empty data

| Status | Meaning | Say |
| --- | --- | --- |
| 403 (inspect) | URL is outside the board's reporting coverage | Which URL, and that only board URLs can be inspected. |
| 409 | Search Console is not connected or access was lost | Connect it in Settings → Google Search Console. |
| 429 | Google quota reached | Try later; report what you already have. |
| 502 | Google rejected the request | Re-check the body with `search`; fix once. |
| 503 | Google Search Console unavailable | No data could be retrieved; do not report zero. |

A 200 with no rows after a recent connection can mean Google has not
recorded impressions yet; say that rather than "no searches".

## Data caveats

- Data is finalized only, so the last two or three days are usually
  missing; use `latestObservedDate` or the overview's
  `dataAvailableThrough` to state the last day covered.
- Search Console keeps about 16 months of query data.
- Rows are top rows; anonymized and low-volume queries are left out, so
  rows do not reconcile with totals.
- In `board_hosts` coverage, Cavuno adds a hostname filter to every query
  and refuses `byProperty` aggregation. Callers cannot widen coverage.
- Cavuno analytics dates are UTC and Search Console dates are
  America/Los_Angeles; say so when a report shows both.

Sources: https://cavuno.com/docs/mcp/reference/search-console,
https://cavuno.com/docs/analytics/connect-search-console,
https://cavuno.com/docs/analytics/dashboard-analytics
