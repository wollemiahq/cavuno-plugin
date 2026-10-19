# Import policy, calibration and run reports

## Contents

- Boundary questions
- Policy template
- Calibration table
- Lifecycle rules
- Run report

## Boundary questions

Ask only the ones whose answer changes which jobs get posted. Propose an
answer from the board model so the user can say yes or correct it.

- Role families, functions and titles to include or exclude.
- Seniority levels and employment types.
- Employer types: direct employers only, or also recruiters, staffing
  agencies, marketplaces, portfolio companies, subsidiaries.
- Geography: countries and regions for on-site and hybrid roles; which
  remote eligibility (worldwide, region, country) qualifies.
- Recency: how old a listing may be, and what proves it is still open.
- Incomplete or low-confidence listings: skip or manual review.
- Publication state: drafts or published, and whether it differs by
  confidence.

## Policy template

```text
Source: <careers page or ATS board URL>   Authorized by: <user, date>
Company on Cavuno: <name> (<company id>)  Hiring company rule: <direct only | agencies allowed>
Must include:   <rule> — e.g. title or description is a solar, storage or grid role
Must exclude:   <rule> — e.g. retail sales, call centre, internships
Manual review:  <rule> — e.g. "analyst" titles with no energy context
Geography:      <countries / remote permits that qualify>
Publication:    <draft | published>; review cases always draft or skipped
Lifecycle:      new = create; changed = show then patch; missing = review; closed = expire after confirmation
Precedence:     a direct user decision beats a keyword rule, which beats inference from existing jobs
```

Add two or three positive and negative examples for each ambiguous rule.

## Calibration table

Show one row per sampled listing; the user corrects decisions, and every
correction becomes a rule or an example.

| Listing | Decision | Rule | Reason |
| --- | --- | --- | --- |
| Senior Battery Engineer, Leeds | include | must include: storage roles | Storage design role, on-site in UK |
| Store Associate, Leeds | exclude | must exclude: retail | Retail role at an energy company |
| Data Analyst | review | manual review: analyst titles | Team not stated |

Check for titles that receive different decisions because of the
employer, description, location or seniority; that is where keyword rules
fail.

## Lifecycle rules

| Seen in this run | Cavuno job | Action |
| --- | --- | --- |
| New `externalId`, qualifies | none | Create (draft or published per policy). |
| Same `externalId`, no material change | exists | Nothing; count as unchanged. |
| Same `externalId`, material change | exists | Show old and new values; patch after confirmation. |
| Missing from the listing pages | exists | Possibly closed: list for review. Pagination, filters or a fetch failure can hide a live job. |
| Listing page says closed or is gone | exists | Propose `/jobs/{id}/expire`; expire after confirmation. |
| Source failed, empty or blocked | any | Change nothing; report the failure. |

Never expire, pause or delete a job because one run did not see it.

## Run report

Report counts and list each non-created item with its reason:

- created (ID, title, status, link)
- updated (ID, fields changed)
- unchanged
- duplicate (existing job ID, matched on)
- excluded (rule)
- manual review (question for the user)
- possibly closed (job ID, last seen)
- skipped (incomplete description, unreadable page)
- failed (error code)

If nothing qualifies, create nothing and say so.

Sources: https://cavuno.com/docs/mcp/cookbook/scrape-jobs-from-a-careers-page,
https://cavuno.com/docs/mcp/cookbook/build-a-custom-job-scraper,
https://cavuno.com/blog/job-scraping, https://cavuno.com/blog/job-wrapping
