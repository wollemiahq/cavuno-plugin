# Fields and collections

## Contents

- Which kind of field
- Job custom fields
- Company and talent profile fields
- Collections and entries
- Connect a collection to profiles or jobs
- Values on records
- Limits

## Which kind of field

| Need | Use |
| --- | --- |
| A value every job poster fills in (clearance level, shift pattern) | Job custom field |
| A value on one company or talent profile (member tier, accreditation) | Profile field |
| A shared list many profiles or jobs pick from (technologies, benefits, certifications) | Collection plus a collection reference field |

On Cavuno-hosted boards, profile fields and collections are not rendered on
public pages, and job custom fields are display only. Boards built with the
AI website builder or the Board SDK can render them and filter by select and
yes/no fields.

## Job custom fields

Endpoint: `PUT /settings/job-form/custom-fields` `{ customFields: [...] }`,
whole-list replace. Types: `short_text`, `long_text`, `rich_text`,
`single_select`, `multi_select`, `boolean`, `number`. Type changes are
rejected (422); renaming a key orphans its values; a deleted private field's
key cannot be reused. Full field rules are in `launch-and-set-up-board`.

## Company and talent profile fields

Read: `GET /settings/profile-fields/company` or `/candidate`. Write: `PUT
/settings/profile-fields/{entity}` `{ definitions: [...] }`, whole-list
replace, array order is display order.

| Key | Notes |
| --- | --- |
| `key` | Fixed once saved |
| `label`, `helpText` | Display text |
| `type` | `short_text`, `long_text`, `rich_text`, `single_select`, `multi_select`, `boolean`, `number`, `date`, `url`, `email`, `phone`, `image_gallery`, `file`. Fixed once saved |
| `options` | Selects only; values store the option key |
| `required`, `min`, `max` | Validation |
| `visibility` | `public` (public API and SDK) or `private` |
| `editableByOwner` | Whether the candidate or an approved company member can edit it |

Visibility and owner editing are independent: a private, owner-editable
field lets the owner supply data that is never published.

Order, shown and required for the company and talent forms (including
built-ins) use `PUT /settings/form-layouts`. Company name and talent name
and email are locked; hiding a company's logo or website can reduce Google
for Jobs visibility.

## Collections and entries

The API calls a collection a custom object type and an entry a record.

| Task | Call |
| --- | --- |
| List collections | `GET /custom-object-types` (`includeArchived` to see archived) |
| Create | `POST /custom-object-types` `{ name, key, definitions, logoFieldKey? }` |
| Update or archive | `PATCH /custom-object-types/{typeId}` `{ name?, definitions?, archived?, logoFieldKey? }` |
| List entries | `GET /custom-object-types/{typeId}/records` |
| Add an entry | `POST /custom-object-types/{typeId}/records` `{ name, values, externalId? }` |
| Edit or archive an entry | `PATCH /custom-object-records/{recordId}`; a supplied `values` object replaces all stored values; `archived` toggles |
| Up to 500 entries at once | `POST /custom-object-types/{typeId}/records/import` `{ records: [...] }`, atomic |
| Larger JSON payload (up to 10 MB) | `POST /custom-object-types/{typeId}/records/import-operations`, then poll `/operations/{id}` for created, updated and failed rows |

Imports update and restore an entry whose `externalId` already exists in the
collection and create the rest. Give every entry an `externalId` so a
re-import does not duplicate. CSV import (up to 8 MB, first 20 rows
previewed) is in the dashboard; images, files and logos are added per entry
in the dashboard.

To give entries logos, add an `image` definition and set `logoFieldKey` to
its key. The record-level `logoUrl` is deprecated.

Entry slugs are created from the name, keep working after a rename, and
can be changed with `slug` (80 characters, lowercase letters, numbers and
single hyphens); the old slug stays reserved for that entry.

Archiving keeps existing selections stored but removes the entry from new
choices, resolved responses and filters.

## Connect a collection to profiles or jobs

- Profiles: `GET` / `PUT /settings/profile-object-references/{entity}` with
  `{ definitions: [...] }`, whole-list replace. Each definition needs `key`,
  `label`, `typeId`, `multiple`, `visibility`, `editableByOwner`,
  `allowOverrides`; optional `maxSelections` (1 to 100), `required`,
  `descriptionFieldKey`, `valueDefinitions` (details per selection),
  `entryDefinitions` (repeatable rows).
- Jobs: collection reference fields on the job form are added in the
  dashboard (Settings → Job form → Custom fields → Add field → Collection
  reference). Jobs then send `collectionValues` (record ID arrays) and
  optional `collectionOverrides`.

## Values on records

| Record | Read | Write |
| --- | --- | --- |
| Job custom fields | `GET /jobs/{id}` | `PATCH /jobs/{id}` `customFieldValues` (additive) |
| Company profile fields | `GET /companies/{id}/custom-fields` | `PATCH /companies/{id}/custom-fields` (additive) |
| Candidate profile fields | `GET /candidates/{id}/custom-fields` | `PATCH /candidates/{id}/custom-fields` (additive) |
| Collection selections | `GET /companies/{id}/object-references`, `/candidates/{id}/object-references` | `PUT` on the same path, whole-list replace |

In additive patches, `null`, `""` or `[]` clears a value; `false` and `0`
are kept. Select values use option keys, never labels.

## Limits

- Job form: 20 custom fields, 50 options per select, 60-character labels,
  20 collection fields, 500 selections per job.
- Profiles: 50 collection reference fields per profile type, 500 selections
  per profile, 100 repeatable rows per selection.
- Account: 100 collections.
- Text: short text 200 characters, long text 2,000, rich text 20,000 after
  sanitizing. Entry names 160 characters, external IDs 200.
- Uploads: 10 images per gallery, 10 MB per file.
- Search filters: 10 clauses per array, 10 values per clause, public fields
  and active entries only.

Sources: https://cavuno.com/docs/profile-fields-and-catalogs,
https://cavuno.com/docs/collection-examples,
https://cavuno.com/docs/board-setup/job-form-settings
