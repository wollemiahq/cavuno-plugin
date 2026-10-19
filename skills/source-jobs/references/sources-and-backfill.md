# Reading sources and working with backfill

## Contents

- Choose the sourcing method
- Read a source well
- Public ATS endpoints
- Respectful fetching
- Backfill through the API
- Dashboard-only sourcing settings

## Choose the sourcing method

| Situation | Method |
| --- | --- |
| Employer is on a supported ATS and the user wants hands-off coverage | Backfill company source (`/backfill/companies/{companyId}/start`, or dashboard: Grow → Backfill → Company sources). |
| Coverage by role across many employers | Backfill target-role rule (`/backfill/rules`, or the dashboard). |
| Partner or ATS publishes a stable public XML feed | Recurring XML feed (dashboard: Backfill → Feeds). |
| Company is "Unsupported" by backfill, or the user wants to review every job | Supervised careers-page import with this skill. |
| One-off file from a partner | Dashboard CSV or XML import (Jobs → Import). |

Backfill dedupes by source URL, expires jobs that disappear at the source,
and does not re-import a job the operator deleted. A careers-page import
done here has none of that automatically, which is why the lifecycle rules
matter.

## Read a source well

1. Prefer the ATS's public JSON endpoint (below) to page HTML.
2. On any job page, look for `<script type="application/ld+json">` with
   `"@type": "JobPosting"`: it carries `title`, `description`,
   `datePosted`, `validThrough`, `hiringOrganization`, `jobLocation`,
   `baseSalary` and `employmentType`.
3. Otherwise read the page text, following pagination and each listing.
4. Take the stable ID from the ATS (job ID, requisition ID) or the listing
   URL, and namespace it: `lever:<id>`, `ashby:<id>`, `site:<url>`.

## Public ATS endpoints

| ATS | Public endpoint | Format |
| --- | --- | --- |
| Greenhouse | `https://boards-api.greenhouse.io/v1/boards/{board_token}/jobs?content=true` | JSON |
| Lever | `https://api.lever.co/v0/postings/{site}?mode=json` | JSON |
| Ashby | `https://api.ashbyhq.com/posting-api/job-board/{job_board_name}?includeCompensation=true` | JSON |
| Workable | `https://www.workable.com/api/accounts/{account_subdomain}?details=true` | JSON |
| Recruitee | `https://{company}.recruitee.com/api/offers/` | JSON |
| Personio | `https://{company}.jobs.personio.de/xml?language=en` | XML |

The token or subdomain is usually visible in the careers page's links to
the ATS. These endpoints list published jobs only; an employer's terms
still decide whether republishing is allowed.

## Respectful fetching

- Check robots rules and the site's terms for the pages you will read.
- Never use someone else's login, shared credentials or session tokens.
- Keep request rates low; one or two requests per second per domain is a
  reasonable ceiling.
- Prefer application URLs to recruiter emails, and do not collect contact
  details for any other purpose.
- Honor removal requests: block the company (the `manage-employers`
  skill) so automated sourcing stops adding it.

## Backfill through the API

| Read | Returns |
| --- | --- |
| `GET /backfill/progress` | `status` (`pending`, `syncing`, `enqueuing`, `enhancing`, `succeeded`, `failed`), `jobsMatched`, `jobsToEnhance`, `jobsEnhanced`, `jobsFailed`, or `null`. |
| `GET /backfill/companies?state=` | Companies in one tab: `backfilling`, `available`, `needs_match` (with match candidates), `not_backfilling`. Optional `search`. |
| `GET /backfill/rules`, `GET /backfill/rules/{id}` | Rule `name`, `matchMode` (`any`/`all`), `rules` (groups of `title` or `description` conditions with `contains_any`, `contains_all` or `is_not` terms), `filters`. |

Every backfill write requires an idempotency key; on the connector pass a
fresh `idempotencyKey` per change (see `cavuno-basics`):

| Write | Tool and body |
| --- | --- |
| New rule | `create` `/backfill/rules` with `{ name, matchMode?, rules, filters? }` |
| Edit or remove a rule | `update` PATCH `/backfill/rules/{id}` (omitted fields are kept), `delete` `/backfill/rules/{id}` |
| Start or stop one company | `create` `/backfill/companies/{companyId}/start` or `/stop`, no body |
| Resolve a `needs_match` company | `create` `/match` with `{ candidateIndex, start }`, or `/mark-no-match` |
| Many companies | `create` `/backfill/companies/bulk-start` or `/bulk-stop` with `{ companyIds }` (up to 100; check each item) |

Rule body: `rules` is a list of cards `{ match: any|all, conditions }`;
each condition is `{ field: title|description, operator: contains_any|contains_all|is_not, terms }`
with 1 to 20 terms; the backfill docs also cap a rule at 50 condition
terms and 30 exclude terms. Exclusions are `is_not` conditions. `filters` takes
`countries`, `subdivisions`, `seniorities` (`entry_level`, `associate`,
`mid_level`, `senior`, `director`, `executive`), `employmentTypes` (the
job enum) and `workplaceTypes` (`remote`, `hybrid`, `onsite`; not the
job field's `on_site`). An empty `rules` array needs at least one filter.
Separate active rules combine as OR.

Start fails with 422 `company_not_backfillable` when no supported ATS is
found for the company; a stale `candidateIndex` returns
`backfill_candidate_conflict`, so re-read `needs_match` and retry. The
dashboard alternative is Grow → Backfill (Add rule, Company sources).

Company sources need the company on the board with its homepage URL (not
the careers page). Target-role rules also add new companies automatically.
Matching jobs usually appear within a few hours; a full pass runs daily.
There is no manual sync.

## Dashboard-only sourcing settings

Backfill settings (Grow → Backfill → More → Backfill settings): maximum
backfilled jobs, board-wide filters, exclude worldwide remote jobs,
posted within N days, sponsored jobs, and the board context (up to 500
characters) used by AI filtering. Recurring XML feeds: up to 10 per board,
checked every 1, 3, 6, 12 or 24 hours. Job moderation for aggregated jobs:
Settings → Features → Require job approval.

Sources: https://cavuno.com/docs/backfill/what-is-backfill,
https://cavuno.com/docs/backfill/configure-backfill,
https://cavuno.com/docs/import-export/recurring-xml-feeds,
https://cavuno.com/docs/import-export/job-import,
https://cavuno.com/blog/ats-platforms-public-job-posting-apis,
https://cavuno.com/blog/job-scraping, https://cavuno.com/blog/job-feeds,
https://cavuno.com/blog/job-board-aggregator, https://cavuno.com/docs/api
