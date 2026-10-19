---
name: refresh-and-prune-content
description: Finds Cavuno blog posts that are losing Google search traffic and decides, post by post, whether to keep, fix metadata, update data, refresh, merge or remove them. Compares each post's recent 28 days in Search Console with its best earlier 28-day window, skips posts published in the last 90 days, ranks by lost clicks, and diagnoses decay versus seasonality, falling demand and click-through loss before any rewrite. Applies approved changes while keeping slugs, and handles merged or removed URLs. Use when the user says "which posts are losing traffic?", "refresh my old blog posts", "why did this post drop?", "clean up outdated articles", "merge these two posts", or "delete old posts and redirect them".
---

# Refresh and prune content

Follow the `cavuno-basics` skill for tool use, confirmations and read-back.

## When to use / not

- Use it for existing posts: finding decline, diagnosing it, and
  refreshing, merging or removing posts.
- New posts or what to write next: `write-blog-post`, `plan-seo-content`.
- This needs Google Search Console connected in the board's settings. If
  the first `query` fails, say so, stop the ranking, and offer a manual
  review of posts the user names.

## Steps

1. **Build the post list.** `read` `/settings` and
   `/domains/{primaryDomainId}` for the domain. `read` `/blog/posts` with
   `status: "published"`, `limit: 100`, following `nextCursor` while
   `hasMore`. Keep `id`, `title`, `slug`, `publishedAt`, `updatedAt`.
   Public URL: `https://<domain>/blog/<slug>`. When the user names
   specific posts by title, find each with `query` `/blog/posts/search`
   and `{ "query": "<title words>" }` instead of listing every post.
2. **Pull daily page data.** `query` `/analytics/search-console/query`
   with `dimensions: ["date", "page"]`, a `page` `contains` `"/blog/"`
   filter and the longest range Search Console returns, ending on
   `cavuno.latestObservedDate`. Page with `startRow` while a response
   fills `rowLimit`. Bodies:
   [references/decay-analysis.md](references/decay-analysis.md).
3. **Measure decay.** Per post: recent clicks (last 28 days) and the best
   earlier 28-day window that ends before the recent one starts. Lost
   clicks = best window minus recent.
4. **Filter, then rank.** Exclude, and list as excluded with the reason:
   posts with `publishedAt` within 90 days of the end date (still ramping);
   posts the user says were substantially rewritten in that time
   (`updatedAt` changes on any edit, so ask rather than infer); posts whose
   best window is too small to matter (state the threshold you used). Rank
   the rest by lost clicks, not percentage drop.
5. **Diagnose the top candidates.** For each, compare the two windows by
   query (`dimensions: ["query"]`, `page` `equals` filter), check the same
   window a year earlier, and compare with the whole blog. Classify with
   the table in
   [references/decay-analysis.md](references/decay-analysis.md):
   seasonality, falling demand, sitewide change, tracking or indexing,
   click-through loss, ranking loss, or intent shift. For a sudden drop to
   zero, `query` `/analytics/search-console/inspect` with the post's
   `inspectionUrl`.
6. **Propose one action per post** and show the evidence table (both
   windows, clicks, impressions, CTR, position, lost and holding queries,
   diagnosis): keep and watch, metadata fix (`seoTitle`,
   `seoDescription`), data update, refresh, merge, unpublish or delete.
   Cheapest fix first: metadata for click-through loss; stale numbers,
   years and examples updated before any rewrite, using fresh job data
   from `query` `/jobs/search`. Wait for the user to pick.
7. **Refresh an approved post.** `read` `/blog/posts/{id}` and snapshot
   title, slug, status, SEO fields, links, images and sections. Check the
   live results with the client's web search, name the information gain,
   show the refreshed outline, then draft section by section, keeping
   sections, queries and links that still work. Show the exact field
   changes and get a yes. `update` with `method: "PATCH"` and only the
   changed fields; never `slug` or `status`.
8. **Merge or remove an approved post.** Follow
   [references/merge-and-prune.md](references/merge-and-prune.md). Blog
   URLs can't be redirected with `/redirects`; tell the user which URL
   will stop working before they confirm. Prefer `create`
   `/blog/posts/{id}/unpublish` (reversible) over `delete`. Each delete
   needs its own confirmation.
9. **Verify and report.** `read` every changed post: same slug and status,
   new fields saved. Report what changed, what was left alone and why, and
   when to re-check: the same 28-day comparison once a full window has
   passed. Don't claim a ranking gain yet.

Done when:

- [ ] Every published post was considered or the stopping point is stated.
- [ ] Posts published within 90 days are listed as excluded, not ranked.
- [ ] Candidates are ranked by lost clicks with both windows shown.
- [ ] Each candidate has a diagnosis before any rewrite was proposed.
- [ ] No write happened without a yes; slugs and status are unchanged on refreshed posts.
- [ ] Every URL that stops working was named to the user before it did.

## Guardrails

- **Search Console rows are top rows.** `cavuno.exhaustive` is false:
  low-volume and anonymized queries are missing, so query rows don't sum
  to page totals. Dates are in `America/Los_Angeles`, and recent days may
  not be final yet; end windows on `latestObservedDate`.
- **Don't rewrite what isn't broken.** A seasonal dip or falling demand is
  a keep. High impressions with falling CTR is a metadata job first.
- **Keep the URL.** Refreshes keep the slug. Change one only when the
  user asks; the old URL then redirects to the new slug automatically.
- **Inspect sparingly.** URL Inspection has quotas; inspect only the URLs
  the diagnosis needs. It reports Google's indexed copy and cannot request
  indexing or run a live test.

## References

- [references/decay-analysis.md](references/decay-analysis.md): Search
  Console request bodies, window math, filters, and the diagnosis table.
- [references/merge-and-prune.md](references/merge-and-prune.md): choosing
  the surviving URL, keeping old URLs working, unpublish versus delete,
  `/redirects` limits.
- [examples/decay-triage.md](examples/decay-triage.md): a ranked decay
  report with one post excluded under 90 days.
