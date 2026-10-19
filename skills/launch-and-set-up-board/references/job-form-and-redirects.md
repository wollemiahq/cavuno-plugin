# Job form and redirects

## Contents

- Read the job form
- Custom job fields
- Form layouts
- What stays in the dashboard
- Redirects

## Read the job form

`GET /settings/job-form` returns built-in field tuning (`salary`,
`seniority`, `workArrangement`, `employmentType` including `customTypes`,
`location`, `sponsorship`) plus `customFields` and `collectionFields`. It
returns `{}` when nothing was configured.

## Custom job fields

`PUT /settings/job-form/custom-fields` with `{ "customFields": [...] }`
replaces the whole list. Array order is display order. An empty array
removes every field.

Each definition:

| Key | Notes |
| --- | --- |
| `key` | Stable slug, used in each job's `customFieldValues`. Never changes. |
| `label` | What posters see; can be renamed later without losing answers. |
| `type` | `short_text`, `long_text`, `rich_text`, `single_select`, `multi_select`, `boolean`, `number`. Cannot change after saving. |
| `options` | For selects only: `{ key, label }`. Jobs store the option `key`. |
| `required` | When true, the value cannot be left empty on a write. Live jobs are not broken; the rule applies at their next edit. |
| `min`, `max` | For `number`. |
| `helpText` | Up to 240 characters. |
| `visibility` | `public` (job page, employers, search, Board API) or `private` (board team only). Omitted keeps the current value. |

Limits from the docs: 20 custom fields per board, 50 options per select,
labels up to 60 characters.

Rules:

1. Read the current list first and include every field you are keeping.
2. Changing `type` on an existing key returns 422
   `settings_custom_field_type_immutable`. To change type, remove the field
   and add a new one with a new key, after the user agrees.
3. Renaming a `key` is a delete plus an add; values under the old key are
   orphaned.
4. A deleted private field's key cannot be reused (400), so its answers stay
   private.
5. On Cavuno-hosted boards custom fields are display only (an "Additional
   details" section on the job page). Boards built with the AI website
   builder or the SDK can also filter by select and boolean fields.
6. Custom fields are filled in by whoever posts the job. They are not
   candidate screening questions; per-job screening questions are not
   supported.

To fill values on existing jobs, `PATCH /jobs/{id}` with
`customFieldValues` keyed by field `key`. Writes are additive; `null`, `""`
or `[]` clears a value; `false` and `0` are kept.

## Form layouts

`GET` / `PUT /settings/form-layouts` read and replace the order, shown and
required state of the `job`, `company` and `talent` forms. Each list sent is
the full ordered form. Nothing is saved unless every list passes; a 400
lists problems in `error.details.issues`.

Locked fields follow Google for Jobs needs: title, description, company,
work arrangement and apply method always show; location is locked while
on-site or hybrid jobs are allowed; candidate locations while remote jobs
are allowed. Hiding employment type or salary is allowed but can reduce
Google for Jobs visibility; say so.

## What stays in the dashboard

Allowed employment types and custom employment types (up to 20), seniority
and work-arrangement options, salary bounds and currencies, office-location
countries, the visa sponsorship question, collection reference fields on
the job form, and field label wording (Website builder, Post a job page).
These are in Settings → Job form.

## Redirects

Use redirects when old URLs from a previous site should keep working.

| Field | Notes |
| --- | --- |
| `fromPath` | Starts with `/`, no domain, no query string. `(.*)` captures a wildcard. |
| `toPath` | A path or a full URL. `%1`, `%2` insert captures in order. |
| `statusCode` | `301` (default) or `302`. Use 301 for a permanent move. |

- One rule: `POST /redirects`. Up to 100 per call: `POST /redirects/batch`
  (5 MB body); check each entry of the response.
- Larger migrations: CSV import in Settings → Redirects (`old_path`,
  `new_path`, up to 1,000 per import). Keep the board under about 1,000
  rules in total.
- Redirects only run on paths that match no Cavuno page. Reserved paths
  cannot be redirected: `/jobs`, `/companies`, `/blog`, `/post`,
  `/password`, `/auth`, `/home`, `/admin`, `/api`, `/boards`, `/_next`,
  `/images`, `/assets`, `/locales`, `/sitemap.xml`, `/robots.txt`,
  `/favicon.ico`, `/rss.xml`.
- Point each old path straight at the final live page; avoid chains.
- Ask the user to test a few sources in a private window, including one
  value per wildcard capture.

Sources: https://cavuno.com/docs/board-setup/job-form-settings,
https://cavuno.com/docs/board-setup/301-redirects,
https://cavuno.com/docs/mcp/reference/settings
