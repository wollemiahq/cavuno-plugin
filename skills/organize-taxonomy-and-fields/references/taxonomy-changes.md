# Taxonomy changes

Categories, skills and markets belong to the board and are writable.
Categories can nest (`parentId`); skills and markets are flat. Each record
has `name`, `slug` and `aliasSlugs`. Remote permits and remote timezones are
global, read-only catalogs (`/taxonomies/remote-permits`,
`/taxonomies/remote-timezones`).

Jobs carry `categories` and `skills` as slug lists. Companies carry
`markets`.

## Pick the least disruptive change

| Goal | Change | Call | Side effects |
| --- | --- | --- | --- |
| Fix wording only | Rename the label | `PATCH /taxonomies/{type}/{id}` `{ name }` | None on URLs or tags |
| Make another spelling resolve | Add an alias | `POST /taxonomies/{type}/{id}/aliases` `{ aliases: [...] }` (1 to 20 per call) | 409 "Alias taken" if another term uses it |
| Change the URL word | Change the slug | `PATCH /taxonomies/{type}/{id}` `{ slug }` | Public URLs move; old slug added to `aliasSlugs` automatically; 409 "Slug taken" |
| Group categories | Nest | `PATCH /taxonomies/categories/{id}` `{ parentId }` | A cycle is rejected (400 `taxonomy_parent_cycle`) |
| Two terms mean the same | Merge | Re-tag, delete the duplicate, alias its slug on the keeper | See below |
| Term is wrong for the board | Delete | `DELETE /taxonomies/{type}/{id}` (204) | See below |

`{type}` is `categories`, `skills` or `markets`.

## Slug changes by type

| Type | What a slug change updates |
| --- | --- |
| Category | Adds the old slug as an alias and updates the slug on tagged jobs |
| Skill | Adds the old slug as an alias and updates the slug on tagged jobs |
| Market | Adds the old slug as an alias; companies keep the old slug and resolve through the alias |

Show the user the old and new public paths that contain the slug. A slug can
appear in filter and landing-page URLs that search engines have indexed.

## Delete effects

| Type | What a delete removes | What is left behind |
| --- | --- | --- |
| Category | The category and its direct child categories | Grandchildren are not removed. Jobs tagged with removed slugs keep them unless re-tagged |
| Skill | The skill | Jobs keep the stale skill slug |
| Market | The market | Companies keep the stale market slug |

A delete can also remove a filter option or landing page from the board.
Before deleting a category, show its children and grandchildren and ask
which should move (`PATCH parentId`) before the delete.

## Merge sequence

1. Pick the keeper (usually the term with more tagged jobs, or the cleaner
   slug).
2. Count jobs on the duplicate: `query` `/jobs/search` with
   `filters.categories` or `filters.skills` set to the duplicate slug, and
   `filters.status` listing every status so drafts and pending jobs count.
   Companies for markets: `/companies/search` with `filters.markets`.
3. Re-tag: for each job, send its full new list with the duplicate replaced
   by the keeper (no repeats). Use `/jobs/batch` with
   `{ id, method: "PATCH", resourceId, body: { skills: [...] } }`, up to 100
   per batch. For companies, `PATCH /companies/{id}` with the new `markets`
   list.
4. Re-run the count; it should be 0.
5. `DELETE` the duplicate.
6. Add the duplicate's slug (and its existing `aliasSlugs`) as aliases on
   the keeper, so old links resolve.
7. Read the keeper back and confirm `aliasSlugs`.

If the user does not want jobs edited, the alternative is to keep both terms
and only rename one; say that deleting without re-tagging leaves stale tags.

## New terms

`POST /taxonomies/{type}` with `{ name }` (required), optional `slug`,
`aliasSlugs`, and for categories `parentId`. Search existing terms first
(`search` query parameter) so you do not create a near-duplicate. Every
slug sent on a job must already exist on the board.

## Structure tips

Set categories up to match the niche before the board has many jobs;
restructuring later touches every tagged job. Keep top-level categories few
and meaningful, use skills for tools and abilities, and keep names in the
board's language. Categories and skills set by hand on a job are not
replaced by automatic tagging; if every category or skill is removed from a
job, automatic tagging may fill it again.

Sources: https://cavuno.com/docs/mcp/reference/taxonomies,
https://cavuno.com/docs/managing-jobs/edit-delete-jobs,
https://cavuno.com/blog/create-a-job-board
