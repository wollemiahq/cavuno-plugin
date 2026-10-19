# Migration plan

## Contents

- Route each data type
- Order of work
- Field mapping
- Cleaning rules
- Verify with counts

## Route each data type

| Data | Best path | Notes |
| --- | --- | --- |
| Jobs, large file | Dashboard: Jobs → Import (CSV or XML, up to 10 MB) | AI maps columns; dedupes by external ID, then application URL plus title, then company, title, location and description. Imports are not held for moderation. |
| Jobs, rows Claude can read | `POST /jobs/batch`, 100 per call | Use when the user pastes rows or the client can read the file. |
| Ongoing partner feed | Dashboard: Backfill → Feeds | See the `source-jobs` skill. |
| Companies, large file | Dashboard: Companies → Import (CSV or XML) | Create-only: an existing company (by domain, then name) is adopted, not overwritten. Logos are downloaded from a `logo` URL. |
| Companies, rows Claude can read | `POST /companies/batch`, 100 per call, 5 MB body | 409 per entry when the domain or name exists. |
| Job alert subscribers | Dashboard only: Job Alerts → Import (CSV with `email`) | Auto-confirmed, no confirmation email, weekly alert. A non-empty `unsubscribed` column skips the row. |
| Employer accounts | Not importable | Employers sign up or are invited; domain match approves them. |
| Candidate accounts and resumes | Not importable through the connector | Ask Cavuno support (hi@cavuno.com) before promising candidates their accounts move. |
| Blog posts | `write-blog-post` skill, as drafts | Redirect their old URLs here. |
| Redirects, large list | Dashboard: Settings → Redirects → Import CSV (`old_path,new_path`, up to 1,000 per import) | |
| Redirects, list Claude can read | `POST /redirects/batch`, 100 per call | |
| Pricing, payments, domain, branding | Dashboard and the other skills | Not part of the data import. |

## Order of work

1. Board settings that affect imported data first: name and contact
   email (`PATCH /settings`), and the job form's currencies and custom
   fields (`/settings/job-form`; built-in options in Settings → Job form).
2. Taxonomy: decide which old categories map to which Cavuno category
   slugs; new terms are created with the `organize-taxonomy-and-fields`
   skill before jobs reference them.
3. Companies, keeping a map of old company ID or name to Cavuno ID.
4. Jobs, referencing that map.
5. Redirects, once new public URLs exist.
6. Subscribers, after the user decides on consent.

## Field mapping

| Old field | Cavuno job field |
| --- | --- |
| Job ID or reference | `externalId`, namespaced (`oldboard:1234`) |
| Company | `companyId` from the map |
| Posted date | `publishedAt` (Unix ms) |
| Expiry date | `expiresAt` (Unix ms); paid listings keep their remaining time |
| Job type | `employmentType` or `customEmploymentType` |
| Remote, hybrid, on-site | `remoteOption` with `officeLocations` or `remotePermits` |
| Category, tags | `categories`, `skills` (existing slugs only) |
| Featured | `isFeatured` |
| Extra fields | `customFieldValues` keyed by `/settings/job-form` keys, or kept in the description |
| Apply link or email | `applicationUrl` |

Company fields: `name` (up to 120 characters), `website` (homepage, not the
careers page), `summary` (280), `description`, `linkedinUrl`, `xUrl`,
`facebookUrl`, `instagramUrl`, `markets`. Logos: after creation, through the
media upload tools in Claude Code, or the dashboard.

## Cleaning rules

- Expired jobs older than about six months usually do not need migrating;
  give their URLs a redirect or let them go, and decide this with the user.
- Merge duplicate reposts of the same job before import.
- Drop companies with no jobs and no employer accounts unless the user
  wants a directory of them.
- Fields the old platform used with no Cavuno equivalent: add a custom
  field first, or fold the value into the description.

## Verify with counts

| Data | Old count | Expected on Cavuno | How to check |
| --- | --- | --- | --- |
| Live jobs | from export | equal, minus agreed drops | `read` `/jobs` with `status: "published"`, paged |
| Companies | from export | equal or fewer (adopted existing) | `read` `/companies`, paged |
| Subscribers | from export | equal, minus skipped rows | `read` `/subscribers/count` |
| Redirects | URL map rows | equal, minus refused | `read` `/redirects`, paged |

Sources: https://cavuno.com/blog/migrate-job-board,
https://cavuno.com/docs/import-export/job-import,
https://cavuno.com/docs/import-export/csv-import-export,
https://cavuno.com/docs/import-export/recurring-xml-feeds,
https://cavuno.com/docs/import-export/data-export,
https://cavuno.com/docs/managing-companies/add-company,
https://cavuno.com/docs/managing-jobs/job-moderation, https://cavuno.com/docs/api
