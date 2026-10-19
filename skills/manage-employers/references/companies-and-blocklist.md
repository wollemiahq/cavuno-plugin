# Companies: delete, block and duplicates

## Contents

- Choose the outcome
- What a block stops
- Delete with an impact preview
- Clean up a duplicate company
- Profile edits that affect URLs

## Choose the outcome

| The user wants | Call | Company profile | Its jobs | Can automated sourcing add it back? |
| --- | --- | --- | --- | --- |
| Gone, and to stay gone | `create` `/companies/{id}/block` `{ remove: true, reason }`, or `delete` `/companies/{id}` with `{ block: "true" }` | Removed; public URL shows not found | Removed | No, until unblocked |
| Keep the page, stop pulling its jobs from feeds | `create` `/companies/{id}/block` `{ remove: false, reason }` | Kept | Published jobs archived; drafts untouched | No |
| A one-off cleanup | `delete` `/companies/{id}` | Deleted permanently | Deleted permanently | Yes, if it still matches the board's sources |
| Bring a blocked company back | `create` `/companies/{id}/unblock` | A removed company returns as an empty profile | Archived or removed jobs are not restored | Yes |

`block=true` on delete is ignored when another company on the board shares
the same website domain; that delete is always plain.

To purge a blocked company completely: unblock, then delete. Deleting it
while blocked keeps it on the blocklist and only removes it from the board.

## What a block stops

Both outcomes stop company backfill for it and reject automated writes that
match it: backfill sync, CSV and XML import, and API, MCP and CLI bulk or
upsert paths. With `remove: true`, employer sign-ups and public job
submissions that match it are rejected too, and it leaves the company
directory, sitemap and search. With the profile kept, the operator,
employers and public submitters can still post for it.

A block matches the company ID, its linked sourcing ID, its normalized
website domain and, for a removed company, its exact slug. Names are not
matched.

The API does not list blocked companies; the dashboard does (Companies →
filter Block status → Blocked, or Backfill → Blocked tab). Keep the IDs you
block in the report.

## Delete with an impact preview

1. `read` `/companies/{id}`: name, website, `jobCount`,
   `publishedJobCount`.
2. `read` `/companies/{id}/jobs` page by page (filter `status` to split
   counts if useful).
3. Show: "Deleting <name> permanently deletes <n> jobs (<p> published, <d>
   drafts, <e> expired). It cannot be undone." List up to five titles.
4. Offer the outcomes above. Mention if the company has employer members
   (`read` `/employers/memberships` with `companyId`): they lose the
   company, though their accounts remain.
5. After a clear yes, run the call, then `read` `/companies/{id}` and
   expect 404 (or, for a block, check the response fields `blocked`,
   `removed`, `removal_scheduled`).

## Clean up a duplicate company

There is no merge. Do this instead, confirming the plan once and the final
delete separately:

1. Find both with `query` `/companies/search`. Choose the survivor: the one
   with the correct website, employer members, more jobs, or the older
   indexed URL.
2. `read` `/companies/{duplicate}/jobs` (all pages).
3. Move each job: `create` `/jobs/batch` with
   `{ id, method: "PATCH", resourceId: "<job id>", body: { "companyId": "<survivor id>" } }`
   operations, up to 100 per batch. Check every entry.
4. Copy profile fields the survivor lacks (summary, description, social
   links, markets) with `update` PATCH `/companies/{survivor}`.
5. Pending claims and members on the duplicate do not move through the
   API. List them so the user can re-invite or approve them on the
   survivor (dashboard: Companies → Manage team).
6. `read` `/companies/{duplicate}/jobs` again; it must be empty. Then
   `delete` `/companies/{duplicate}` with no block: the duplicate often
   shares the survivor's domain, and a block on that domain would stop
   sourcing for the survivor too.
7. Tell the user the duplicate's public URL now shows not found.

## Profile edits that affect URLs

- `slug` sets the public URL `/companies/<slug>`. After a change the old
  URL redirects to the new one. Do not add a `/redirects` entry for it:
  `/companies` paths are reserved and rejected there.
- Changing `website` re-runs enrichment, which fills only empty fields,
  and changes which domain blocking and employer email matching use.
- `website` should be the homepage, not the careers page; backfill finds
  the careers page from it.

Sources: https://cavuno.com/docs/mcp/reference/companies,
https://cavuno.com/docs/managing-companies/company-blocklist,
https://cavuno.com/docs/managing-companies/edit-company-profile,
https://cavuno.com/docs/managing-companies/add-company,
https://cavuno.com/docs/managing-companies/company-markets,
https://cavuno.com/docs/api
