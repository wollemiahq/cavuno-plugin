# Example: first supervised import from a careers page

## Input

> Import the jobs from https://northwindstorage.example/careers onto my
> board as drafts.

## Plan

1. Ask: "Is your organization allowed to republish Northwind Storage's
   listings?" The user says Northwind asked them to list its roles.
2. `query` `/companies/search` `{ query: "Northwind Storage" }`: the
   company exists, `canBackfill: false`. Backfill cannot cover it, so a
   supervised import it is.
3. Board model from `/settings`, top-level categories and 25 published
   jobs: UK clean-energy engineering and project roles, mostly mid-level
   and senior, on-site or hybrid, some UK-only remote.
4. Policy agreed with the user: include engineering, project and
   operations roles in the UK; exclude sales and internships; review
   "analyst" titles; drafts; missing jobs go to review.
5. The careers page links to `jobs.ashbyhq.com/northwind`, so the client
   reads `https://api.ashbyhq.com/posting-api/job-board/northwind?includeCompensation=true`.
   Calibration on a sample of listings:

| Listing | Decision | Rule |
| --- | --- | --- |
| Battery Systems Engineer, Sheffield | include | engineering, UK |
| Field Service Technician, Leeds | include | operations, UK |
| Inside Sales Representative | exclude | sales |
| Commercial Analyst | review | analyst titles |

The user moves "Commercial Analyst" to exclude; the policy gains an
example.

## Run plan shown to the user

> 14 open listings: 9 qualify, 2 are already on the board (same
> `externalId`), 3 excluded. All 9 will be drafts at Northwind Storage
> (existing company). Sample: Battery Systems Engineer (Sheffield, hybrid),
> Field Service Technician (Leeds, on-site), ... Create them?

## Write and report

After "yes": `create` `/jobs/batch` with 9 create operations, each with
`externalId` `ashby:<job id>`. All 9 entries return 201. Each job is read
back.

> Created 9 drafts (IDs and links below). Skipped 2 already on the board
> and 3 excluded (sales, internship, commercial analyst). Next run: new listings are
> created as drafts, changes are shown before updating, and anything
> missing from the page is listed for you to check rather than expired.
