# Merging and pruning posts

## How blog URLs redirect

- **Slug history.** When a post's `slug` changes through
  `PATCH /blog/posts/{id}`, Cavuno records the old slug and
  `/blog/<old-slug>` redirects permanently to the post's current slug. A
  custom frontend built on the board SDK gets `redirected` and `newSlug`
  and must issue the redirect itself.
- **`/redirects` cannot cover blog URLs.** `/blog` is a reserved path, so
  `POST /redirects` with a `fromPath` under `/blog/` is rejected as
  conflicting with a reserved route. Don't retry it; use slug history or
  accept the 404.
- **`/redirects` still helps** for old non-blog paths, such as
  `/articles/old-post` from a previous platform, pointing to
  `/blog/<slug>`. Body: `fromPath`, `toPath`, optional `statusCode` (301,
  the default, or 302). Check `read` `/redirects` with `search` (a
  `fromPath` prefix) first so you don't add a duplicate.
- **Deleted and unpublished posts return 404.** Deleting a post also
  removes its slug history, so its own older URLs stop redirecting.

## Merge two posts

Pick the surviving URL: the post with more clicks over the best window,
more working queries and more inbound links. Fold the other post's unique,
still-useful sections into it; drop what repeats.

| Goal | Steps | Result |
| --- | --- | --- |
| Simple: keep the survivor's URL, retire the other | PATCH the survivor's `html` (and fields) with the merged article; then unpublish the other post | Survivor URL serves the merged post; the retired URL returns 404 |
| Keep both URLs working | Put the merged article into the post being retired (B), copying the survivor's (A) authors, tags, cover and SEO fields; delete A; immediately PATCH B's `slug` to A's old slug | A's URL serves the merged post; B's old URL redirects to it. A's own older slugs stop redirecting, and the post keeps B's `publishedAt` |

The second route includes a delete, so it needs its own confirmation, and
A's URL is briefly unavailable between the delete and the slug change.
Show both routes and their trade-offs; let the user choose.

After a merge, update internal links in other posts that pointed to the
retired URL (show each before/after sentence; PATCH only `html`).

## Prune a post

A post is a prune candidate when it earns no meaningful clicks or
impressions, has no unique content worth merging, and no links worth
keeping. Before proposing it:

1. Check its Search Console data for the whole range, not only the recent
   window.
2. Check internal links to it from other posts (read their `html`).
3. Decide between: merge into a related post, rewrite, unpublish, or
   delete.

| Action | Call | Reversible |
| --- | --- | --- |
| Unpublish | `create` `/blog/posts/{id}/unpublish` (no body) | Yes; republish keeps the original `publishedAt` |
| Delete | `delete` `/blog/posts/{id}` (204) | No |

Default to unpublish. Delete only on an explicit request, one confirmation
per post, after naming the URL that will return 404. Remove or replace
internal links to a pruned post.

Sources: https://cavuno.com/docs/board-setup/301-redirects,
https://cavuno.com/docs/sdk/reference/blog,
https://cavuno.com/docs/mcp/reference/blog-posts,
https://cavuno.com/docs/mcp/cookbook/refresh-blog-posts-with-search-console
