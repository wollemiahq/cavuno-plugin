# First-party job data for a post

The board's own jobs are the information gain other sites can't copy. The
AEO guide's pattern: converge on what is already known, then diverge with
your own data. The E-E-A-T guide calls this data-driven experience.

## Pull the jobs

`query` `/jobs/search`:

```json
{ "filters": { "status": ["published"], "categories": ["solar-installer"] },
  "limit": 100 }
```

- Filter values are canonical slugs: get them from `read`
  `/taxonomies/categories` and `/taxonomies/skills`. Each filter array
  takes up to 10 values; values in one array match any.
- Other filters: `seniority`, `remoteOption`, `employmentType`,
  `companyId`, `publishedAt: { gte, lte }` for a date range. A free-text
  `query` (up to 200 characters) narrows further.
- Page with `cursor: nextCursor` while `hasMore`. State the total you
  read.

Each result carries `title`, `companyId`, `categories`, `skills`,
`seniority`, `remoteOption`, `employmentType`, `salaryMin`, `salaryMax`,
`salaryCurrency`, `salaryTimeframe` and `publishedAt`. Location is not in
search results: `read` `/jobs/{id}` for `officeLocations` (city, region,
country), so limit location analysis to a stated sample.

## What to derive

| Data point | How | Write it as |
| --- | --- | --- |
| Salary range | Jobs with both `salaryMin` and `salaryMax`; group by `salaryCurrency` and `salaryTimeframe` (never mix `per_year` with `per_hour`); report low, median and high of the ranges | "Across 41 salaried senior roles on the board in October 2026, advertised ranges ran from..." |
| Who is hiring | Count jobs per `companyId`; `read` `/companies/{id}` for names | "The most active employers this month were..." |
| Demand by category or skill | Count jobs per slug in `categories` or `skills` | Share of the jobs read, with the total |
| Remote share | Count `remoteOption` values | "x of y roles were remote" |
| Demand by location | `officeLocations` from a sample of job details | Name the sample size |
| Seniority mix | Count `seniority` | Entry level vs senior counts |

## Rules

- Every figure carries its base: the number of jobs and the date read.
- With few jobs (a handful), give the actual roles or ranges instead of a
  median, and say the sample is small.
- Jobs without a salary are excluded from salary figures; say how many.
- Don't present advertised salary ranges as what people earn; they are
  what employers on this board advertised.
- Don't name candidates or quote applications. Company names on public
  jobs are fine.
- External figures (government labor statistics, surveys) need the
  source name and a link the reader can check.

Sources: https://cavuno.com/blog/aeo-for-job-boards,
https://cavuno.com/blog/eeat-for-job-boards,
https://cavuno.com/blog/local-seo-for-job-boards,
https://cavuno.com/blog/link-building-for-job-boards,
https://cavuno.com/docs/mcp/reference/jobs
