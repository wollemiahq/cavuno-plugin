---
name: plan-seo-content
description: Plans what a Cavuno job board blog should publish, refresh, merge or skip next. Inventories every existing post, groups search demand into topics (one strong page per topic rather than one post per keyword), finds real gaps, checks Google Search Console for queries split across several posts, and returns a prioritized plan with evidence for each item. Writes nothing to the board. Use when the user says "plan my blog content", "what should we write next?", "find content gaps in our blog", "build a content calendar for the board", "do keyword research for my job board", or "are any of my posts competing with each other?".
---

# Plan SEO content

Follow the `cavuno-basics` skill for tool use, confirmations and read-back.

## When to use / not

- Use it to decide what content to create or change: keyword and topic
  research, gap analysis, cannibalisation checks, a publishing roadmap.
- Writing one post, from outline to draft: `write-blog-post`.
- Posts that are losing search traffic: `refresh-and-prune-content`.
- The output is a decision document. Nothing is saved to the board.

## Steps

1. **Learn the board.** `read` `/settings` (board name, `primaryDomainId`)
   and `/domains/{primaryDomainId}` for the public domain. `read`
   `/taxonomies/categories` and `/taxonomies/skills`. `query` `/jobs/search`
   with `{ "filters": { "status": ["published"] }, "limit": 100 }` and
   tally `categories`, `seniority`, `remoteOption`, `companyId` and which
   jobs carry salaries. From this, state the niche and the two audiences
   (job seekers, employers). Ask only for what you cannot discover, usually
   the business goal: employer revenue, seeker traffic, newsletter growth.
2. **Inventory every post.** `read` `/blog/posts` with `limit: 100`; while
   `hasMore` is true, repeat with `cursor: nextCursor`. Keep one compact row
   per post: `id`, `title`, `slug`, `status`, `publishedAt`, `updatedAt`,
   `customExcerpt`, `tagIds`. Include drafts and scheduled posts so you do
   not plan work already in progress. The list has no body; `read`
   `/blog/posts/{id}` for `html` only for posts that overlap a topic you
   are judging, a few at a time. Public URL: `https://<domain>/blog/<slug>`.
3. **Collect demand evidence, labeled by source.**
   - Search Console (first-party): `query`
     `/analytics/search-console/query`; bodies are in
     [references/search-console-recipes.md](references/search-console-recipes.md).
     If the call fails, say Search Console is not available and carry on.
   - Search volume and difficulty: only from the user's own keyword tools
     (Ahrefs, Semrush, Keyword Planner or an export they paste). Cavuno does
     not provide them. Name the tool next to every number.
   - Intent: the client's web search or browser, if it has one. Read the
     top results, related searches and community questions.
   With no keyword tool, write "volume not measured", never an estimate.
4. **Group demand into topics.** Merge variants that share an intent into
   one topic owned by one page. Split only when the reader expects a
   different answer or format. Record the page type the results reward
   (guide, list, template, salary data, job listings). Rules:
   [references/topic-planning.md](references/topic-planning.md).
5. **Subtract existing coverage.** Mark each topic: covered well, thin,
   outdated, split across posts, missing, or better served by a page Cavuno
   already generates (job, company, category, skill and location pages).
   Before marking a topic missing, `query` `/blog/posts/search` with
   `{ "query": "<key term>" }` for one or two of its key terms; it matches
   titles across drafts, scheduled and published posts. A post titled
   differently can still cover the topic, so check the inventory too.
   Never plan an article to rank for "[role] jobs in [city]".
6. **Check cannibalisation.** Run the query-by-page recipe. For each query
   where two or more posts both earn meaningful impressions, read both
   posts and decide with the table in
   [references/topic-planning.md](references/topic-planning.md): merge,
   differentiate, or leave alone. Two posts sharing a query is a lead, not
   a verdict.
7. **Decide and prioritize.** Give every topic one action: new article,
   refresh, merge, expand a section, improve a non-blog page, or skip. Rate
   business value and search viability separately, then information gain,
   effort and dependencies. A topic with no concrete information gain
   (a data point from the board's jobs, a template, a decision rule, an
   operator's experience) is a skip, however large its volume.
8. **Deliver the plan.** One table, highest priority first, dependencies
   before the items that rely on them (a merge before the new posts that
   will link to it). Columns: topic, reader, intent and page type,
   evidence with source, existing posts (title and URL), action,
   information gain, business value, effort, priority. Then offer the next
   step: `write-blog-post` for the first new item,
   `refresh-and-prune-content` for refresh and merge items.

Done when:

- [ ] Every post page was read (`hasMore` false) or the stopping point is stated.
- [ ] Every number in the plan names its source; none is estimated.
- [ ] Each topic maps to one page, existing or planned, and keyword
      variants are grouped, not listed as separate posts.
- [ ] Every planned item lists the existing posts it touches and its
      cannibalisation status.
- [ ] Every new or refresh item names a concrete information gain.
- [ ] No `create`, `update` or `delete` call was made.

## Guardrails

- **Planning is read-only.** Don't create tags, drafts or redirects while
  planning, even to "get started". Hand approved items to the sibling
  skills.
- **Search Console rows are top rows, not a full export.** Low-volume and
  anonymized queries are missing, so query rows don't add up to page
  totals. Dates are in `America/Los_Angeles`. Report what the data shows
  and keep that caveat.
- **Match the page type to the intent.** If the results are job listings,
  directories or tools, an advice post is the wrong answer; say which board
  page should win instead.
- **Fit the call to action to the reader.** Don't bolt "post a job" onto a
  job seeker topic; the natural next step is a job search or job alert.

## References

- [references/topic-planning.md](references/topic-planning.md): topic
  clustering, page-type mapping, cannibalisation and action decision
  tables, prioritization and cadence, with what the Cavuno posts say.
- [references/search-console-recipes.md](references/search-console-recipes.md):
  request bodies for query, page and query-by-page reads, and how to read
  the response.
- [examples/quarterly-plan.md](examples/quarterly-plan.md): a worked plan
  for a small niche board.
