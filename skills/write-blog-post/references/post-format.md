# Post format and publication checks

## Contents

- Fields on `POST /blog/posts`
- HTML body
- Headings
- Answer-shaped sections
- Trust signals
- Internal links
- Publication check

## Fields on `POST /blog/posts`

| Field | Limit | Notes |
| --- | --- | --- |
| `title` | required, 200 chars | Sentence case. |
| `slug` | 120 chars, `^[a-z0-9-]+$` | Short, descriptive, no dates unless the post is dated. |
| `html` | 1 MB | The body. Sanitized server-side. |
| `customExcerpt` | 500 chars | One sentence on what the reader learns. |
| `seoTitle` | 150 chars | The search result title; set it explicitly. |
| `seoDescription` | 320 chars | The promise of the post, with the main query. |
| `authorIds` | up to 10 | IDs from `/blog/authors`. |
| `tagIds` | up to 20 | IDs from `/blog/tags`. |
| `coverMediaId`, `featureImageAlt`, `featureImageCaption` | alt 200, caption 500 | Media ID from a completed upload, never a URL. |
| `readingTimeMin` | 0-1000 | Optional. |
| `status` | `draft`, `scheduled`, `published` | Omit: new posts default to `draft`. |
| `publishedAt` | date-time | With `status: "scheduled"` and a future date, schedules the post. |

`PATCH /blog/posts/{id}` takes the same fields. A `status` change through
PATCH is rejected (`blog_invalid_transition`) except scheduling; use
`/blog/posts/{id}/publish` and `/unpublish`. Posts returned by the API
carry `authorIds` and `tagIds`, not embedded objects.

## HTML body

- Use `<h2>`, `<h3>`, `<p>`, `<ul>`/`<ol>`, `<table>`, `<a href>`,
  `<strong>`, `<blockquote>`. No `<h1>`, scripts or inline styles.
- Internal links use the board path, `/blog/<slug>`, or the board page's
  path; check how existing posts link and match it.
- External links point to the original source of a claim.

## Headings

Sentence case for every heading: capitalize the first word, proper nouns
(Cavuno, Indeed, Texas) and acronyms (SEO, ATS) only.

- Right: "How much do solar installers earn in Texas?"
- Wrong: "How Much Do Solar Installers Earn In Texas?"

## Answer-shaped sections

From the AEO guide:

- Front-load the answer: the first 100 words after a heading should
  answer the question the heading implies.
- Phrase headings the way readers ask: "What does a vet tech do?" over
  "Responsibilities".
- A self-contained answer passage names the question, answers it, and
  gives enough context to check it. 40-80 words is an editing exercise,
  not a rule; no passage length guarantees citation.
- Lists of 3-10 items, each a complete thought.
- Put numbers and named sources in the text; specific figures with a
  source and date are what answer engines quote.
- One page per cluster of related questions: answer the head, mid-tail
  and long-tail questions on the topic in one post.

The post's main promise is answered in its opening paragraph, not after a
preamble.

## Trust signals

From the E-E-A-T guide, job boards earn experience four ways; use at least
one per post:

- operator experience (what running the board shows),
- community experience (seeker or employer outcomes, with permission),
- an expert contributor or reviewer with a visible byline,
- the board's own data.

Give the post a real author from `/blog/authors` with a bio. Don't
attribute quotes or experiences to people who didn't give them.

## Internal links

For each link, record source, destination, anchor and purpose
(definition, next step, related guide, board page). Skip self-links,
repeated links to the same destination, generic anchors such as "click
here", and links that don't help the reader at that point. Link
editorial posts to the board pages that convert: the matching category,
skill or location page, or the job alert signup.

## Publication check

Before saving, confirm:

- title, slug, excerpt, SEO title and description fit the limits above;
- every number has its base or source;
- every link resolves to a published post or live page;
- author and tags are set;
- no notes about keywords, SEO or the writing process remain in the body.

Sources: https://cavuno.com/docs/mcp/cookbook/create-and-publish-a-blog-post,
https://cavuno.com/docs/mcp/cookbook/improve-internal-linking-in-your-blog,
https://cavuno.com/docs/mcp/reference/blog-posts,
https://cavuno.com/docs/mcp/reference/blog-authors-and-tags,
https://cavuno.com/docs/mcp/reference/media,
https://cavuno.com/blog/aeo-for-job-boards,
https://cavuno.com/blog/eeat-for-job-boards
