---
name: organize-taxonomy-and-fields
description: Organizes the structure of a Cavuno job board - job categories, skills and company markets, plus job custom fields, company and talent profile fields, and collections of shared entries such as technologies or benefits. Previews the impact of every change first (jobs and companies tagged, child categories that a delete removes, public URLs a slug change moves), prefers renames and aliases over deletes, and merges duplicates without losing tags. Use when the user says "clean up our categories", "merge Dev Ops into DevOps", "rename the Marketing category", "delete unused skills", "nest Frontend under Engineering", "add a member tier field to company profiles" or "set up a Technologies collection".
---

# Organize taxonomy and fields

Follow the `cavuno-basics` skill for tool use, confirmations and read-back.

## When to use / not

Use this skill to change the board's vocabulary and data shape. Not for:

- tagging or editing individual jobs as part of posting: `post-jobs`
- the job form's built-in fields, order and required state during setup:
  `launch-and-set-up-board` (this skill covers custom fields once the board
  is live, too; either is fine)
- company records and employer accounts: `manage-employers`
- landing pages and SEO content built on categories: `plan-seo-content`

## Steps

1. **Read the current vocabulary.**
   - Categories: `read` `/taxonomies/categories/tree` (nested `children`;
     capped at 1,000 nodes, above which it returns 400, so page
     `/taxonomies/categories` instead).
   - Skills and markets: page `/taxonomies/skills` and
     `/taxonomies/markets` with `limit: 100`. With `search`, a page can be
     short or empty while `hasMore` is true; keep paging until it is false.
   - Fields: `read` `/settings/job-form`, `/settings/profile-fields/company`,
     `/settings/profile-fields/candidate`, `/custom-object-types`.
2. **Measure impact** for every term you plan to rename, merge or delete:
   - jobs: `query` `/jobs/search` with
     `{ filters: { categories: ["<slug>"], status: ["draft", "published",
     "pending_approval", "expired", "archived"] } }` (or `skills`),
     `limit: 100`, so drafts and pending jobs are counted too,
     paging to count (report "100+" past the first page if counting more is
     not needed);
   - companies: `query` `/companies/search` with
     `{ filters: { markets: ["<slug>"] } }`;
   - for a category delete, list its direct children and their children
     from the tree.
3. **Choose the change** with
   [references/taxonomy-changes.md](references/taxonomy-changes.md). Prefer,
   in order: rename the label, add an alias, change the slug, merge, delete.
4. **Show the plan** as a table: term, change, jobs and companies affected,
   URLs that move, children affected, and a sample of up to five affected
   jobs (title and ID). Deletes and slug changes each get their own
   confirmation.
5. **Apply in the safe order.** For a merge: re-tag affected jobs with
   `/jobs/batch` `PATCH` sub-operations (up to 100 per batch, each sending
   the job's complete new `categories` or `skills` list), check every entry,
   then `delete` the duplicate, then `create`
   `/taxonomies/{type}/{keepId}/aliases` `{ aliases: ["<old slug>"] }` so the
   old slug still resolves. For a category delete, re-parent children the
   user wants to keep (`PATCH` `parentId`) first.
6. **Fields and collections.** Follow
   [references/fields-and-collections.md](references/fields-and-collections.md).
   Definition writes (`PUT /settings/job-form/custom-fields`, `PUT
   /settings/profile-fields/{entity}`, `PUT
   /settings/profile-object-references/{entity}`) replace the whole list:
   read it, add the change, show the full list, then send it.
7. **Read back.** Re-read the tree or list, re-run the impact queries (the
   old term should match 0 jobs after a merge), and report.

Done when:

- [ ] Every renamed, merged or deleted term had its impact counted before
      the write, and the counts were shown.
- [ ] No term with tagged jobs or companies was deleted without re-tagging
      or the user's explicit choice to leave stale tags.
- [ ] Each delete and slug change had its own confirmation.
- [ ] Every whole-list definition write included every field being kept.
- [ ] Read-backs match the plan; batch failures are listed with error codes.

## Guardrails

- **A category delete is wider than it looks.** It deletes the category and
  its direct children; grandchildren are not deleted. Name every child in
  the plan.
- **Skill and market deletes leave stale slugs.** Jobs keep a deleted
  skill's slug and companies keep a deleted market's slug. Re-tag first.
- **Slug changes move public URLs.** The old slug is added as an alias
  automatically. Category and skill slug changes update tagged jobs; a
  market slug change does not update companies, which rely on the alias.
- **Keys and types are permanent.** Field keys, collection keys and saved
  field types cannot change. Choose keys that describe the data, not the
  current wording.
- **Archive collections; don't delete them.** There is no delete for
  collections or entries; `PATCH` `archived: true` hides them from new
  choices and filters.
- **Remote permits and timezones are read-only** global catalogs. Use their
  values; never try to create one.

## References

- [references/taxonomy-changes.md](references/taxonomy-changes.md): decision
  table for rename, alias, slug change, nest, merge and delete, with each
  side effect.
- [references/fields-and-collections.md](references/fields-and-collections.md):
  job custom fields, profile fields, collections and records, imports and
  limits.
- [examples/merge-duplicate-skills.md](examples/merge-duplicate-skills.md): a
  merge from impact count to verified alias.
