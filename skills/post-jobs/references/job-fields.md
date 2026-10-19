# Job fields for `POST /jobs`

## Contents

- Required fields
- Mapping source text to fields
- Work arrangement and location
- Taxonomy and custom fields
- Validation errors and repairs

## Required fields

`title` (up to 200 characters), `description`, `applicationUrl`,
`remoteOption`, and exactly one of `companyId` or `company`. Sending both
is an error. Confirm the schema with `search` if a 400 names a field you
did not expect.

## Mapping source text to fields

| Source says | Field | Value |
| --- | --- | --- |
| Job title | `title` | As written by the employer, including seniority. |
| Full posting | `description` | HTML up to 25,000 characters. Unsupported tags are stripped; plain text becomes paragraphs. |
| Apply link or inbox | `applicationUrl` | HTTPS URL, `mailto:` URI or bare email. Use the direct employer or ATS link, not a job-board wrapper. |
| ATS requisition ID, or none | `externalId` | Requisition ID when shown, otherwise the source job URL. Up to 255 characters. For one source with many jobs, namespace it, for example `greenhouse:4567`. |
| Full-time, contract... | `employmentType` | `full_time`, `part_time`, `contract`, `internship`, `temporary`, `volunteer`, `other`. A board-specific type goes in `customEmploymentType` (keys from `/settings/job-form`). |
| Junior, Senior, Head of... | `seniority` | `entry_level`, `associate`, `mid_level`, `senior`, `lead`, `principal`, `director`, `executive`. |
| Pay range | `salaryMin`, `salaryMax`, `salaryCurrency`, `salaryTimeframe` | ISO 4217 currency; timeframe `per_year`, `per_month`, `per_week`, `per_day`, `per_hour`. |
| Degree required | `educationRequirements` | `high_school`, `associate_degree`, `bachelor_degree`, `professional_certificate`, `postgraduate_degree`, `no_requirements`. |
| "3+ years" | `experienceMonths` | Months, for example 36. |
| Visa sponsorship | `remoteSponsorship` | `yes`, `no`, `unknown`. |
| Closing date | `expiresAt` | Unix milliseconds on create. Omitted means 30 days from creation. |

Salary in the description text is also extracted by Cavuno's enrichment, so
set the salary fields only when the source states a range clearly.

## Work arrangement and location

| `remoteOption` | Also required | Notes |
| --- | --- | --- |
| `on_site` | at least one `officeLocations` entry | |
| `hybrid` | at least one `officeLocations` entry | Optional `inOfficeFrequency` with `inOfficePeriod` (`per_week`, `per_month`, `per_year`). |
| `remote` | `remotePermits` | Use `[{ "type": "worldwide", "value": "worldwide" }]` only when the source says anywhere. Worldwide cannot be combined with other entries. |

- `officeLocations`: prefer `{ "query": "City, Country" }`. Use
  `{ city, country, region?, locality? }` only when every part is known.
- `remotePermits` types: `worldwide`, `world_region` (EMEA, LATAM, NA,
  APAC), `continent`, `region`, `subregion`, `subdivision` (ISO 3166-2),
  `country` (ISO 3166-1 alpha-2), `custom` (for example `EU`). Take values
  from `GET /taxonomies/remote-permits`; you cannot create new ones.
- `remoteTimezones`: omit on create and Cavuno derives it from the
  permits. Send it only when the source names a time zone overlap, using
  values from `GET /taxonomies/remote-timezones` (`timezone` entries accept
  `plusMinus` of 0 to 12 hours). A later PATCH never re-derives it.
- "Remote (US only)" is `remote` with a `country` permit `US`, not a
  worldwide permit.

## Taxonomy and custom fields

- `categories` and `skills` take up to 100 existing slugs each. An unknown
  slug fails the request. Leave them out rather than forcing a weak match;
  Cavuno's automatic tagging fills them, and values you set by hand are not
  replaced by it.
- `customFieldValues` is keyed by field `key` from `/settings/job-form`.
  `single_select` and `multi_select` take option keys, not labels. A
  wrong type fails with `custom_field_wrong_type`.
- New categories, skills or fields belong to the
  `organize-taxonomy-and-fields` skill, not to this task.

## Validation errors and repairs

| Error | Repair |
| --- | --- |
| 400 naming `remoteOption`, `officeLocations` or `remotePermits` | Apply the table above, then retry once. |
| 400 `jobs_unresolvable_location` | Retry with `{ city, country }`, then `{ query: "City, Country" }`. If it still fails, ask the user. |
| 400 naming `salaryCurrency` or `salaryTimeframe` | Ask for the missing part, or drop the salary fields. |
| 404 on `companyId` | Look the company up again; never guess an ID. |

Sources: https://cavuno.com/docs/mcp/reference/jobs,
https://cavuno.com/docs/mcp/reference/taxonomies,
https://cavuno.com/docs/mcp/cookbook/post-a-job,
https://cavuno.com/docs/mcp/cookbook/build-a-custom-job-scraper,
https://cavuno.com/docs/managing-jobs/create-job-posting,
https://cavuno.com/docs/managing-jobs/edit-delete-jobs, https://cavuno.com/docs/api
