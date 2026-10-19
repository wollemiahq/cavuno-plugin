# Example: one job from a URL

## Input

> Add this to the board: https://boards.greenhouse.io/tidalgrid/jobs/4471

## Plan

1. Read the page with the client's web fetch. It is the employer's own
   Greenhouse listing, so the description is complete.
2. Extract: "Senior Grid Integration Engineer", Tidal Grid
   (`tidalgrid.example`), hybrid in Glasgow, 3 days a week in office, full
   time, senior, GBP 70,000 to 85,000 per year.
3. `read` `/taxonomies/categories` with `search: "engineering"`: slug
   `engineering` exists. `read` `/taxonomies/skills` with
   `search: "power systems"`: slug `power-systems` exists.
4. `query` `/companies/search` `{ query: "Tidal Grid" }`: one result with
   website `tidalgrid.example`. Reuse it.
5. `read` `/jobs` `{ externalId: "greenhouse:4471" }` and
   `{ applicationUrl: "https://boards.greenhouse.io/tidalgrid/jobs/4471" }`:
   no matches.

Shown to the user:

| Field | Value |
| --- | --- |
| Title | Senior Grid Integration Engineer |
| Company | Tidal Grid (existing) |
| Work arrangement | Hybrid, Glasgow, United Kingdom, 3 days per week |
| Employment type, seniority | Full time, senior |
| Salary | 70,000 to 85,000 GBP per year |
| Categories, skills | engineering; power-systems |
| Apply | https://boards.greenhouse.io/tidalgrid/jobs/4471 |
| External ID | greenhouse:4471 |
| Status | Draft |

"Create this as a draft?"

## Write, after "yes"

`create` `/jobs`:

```json
{
  "companyId": "cmp_8h2k",
  "title": "Senior Grid Integration Engineer",
  "description": "<p>Tidal Grid is ...</p>",
  "applicationUrl": "https://boards.greenhouse.io/tidalgrid/jobs/4471",
  "externalId": "greenhouse:4471",
  "remoteOption": "hybrid",
  "officeLocations": [{ "query": "Glasgow, United Kingdom" }],
  "inOfficeFrequency": 3,
  "inOfficePeriod": "per_week",
  "employmentType": "full_time",
  "seniority": "senior",
  "salaryMin": 70000,
  "salaryMax": 85000,
  "salaryCurrency": "GBP",
  "salaryTimeframe": "per_year",
  "categories": ["engineering"],
  "skills": ["power-systems"]
}
```

## Report

`read` `/jobs/{id}` confirms status `draft`, the company, the location and
a description of the same length as the source after sanitization.

> Created a draft: Senior Grid Integration Engineer at Tidal Grid
> (`job_51q`). Review it at <links.admin>. Say "publish it" when it looks
> right and I will publish it and send the public link.
