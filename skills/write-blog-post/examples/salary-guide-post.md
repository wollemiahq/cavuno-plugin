# Example: a salary post from the board's own jobs

## Input

"Write a post on how much solar installers make, for my climate jobs
board."

## Checks before the outline

- `/blog/posts/search` for "solar installer salary" and "installer pay":
  no post on installer pay; "How to become a solar installer" exists and
  will link to the new post.
- Search Console, filter query contains "solar installer salary": the
  board gets impressions only on the category page, not a post.
- Live results: salary aggregators and guides; a guide with data fits.
- `/jobs/search` with `categories: ["solar-installer"]`: 38 published
  jobs, 22 with a yearly USD range, 4 with an hourly range.

## Outline shown for approval

- Title: "Solar installer salary: what employers on our board pay"
- Slug: `solar-installer-salary`
- Reader: someone deciding whether to train as an installer.
- Information gain: advertised ranges from 22 current yearly-salaried
  jobs, split by seniority, and the employers posting most often. The top
  results quote national averages without current postings.
- `<h2>` How much do solar installers make? (answer in the first
  paragraph, with the job count and month)
- `<h2>` How pay changes with experience
- `<h2>` Which companies are hiring installers now
- `<h2>` Hourly vs salaried installer roles (4 hourly jobs: listed, not
  averaged)
- `<h2>` How to get your first installer job (links to
  `/blog/how-to-become-a-solar-installer`)
- Call to action: the solar installer job alert.

"Shall I draft it from this outline?"

## After approval

Drafted section by section in HTML, sentence-case headings, every figure
stated as "of the 22 salaried jobs on the board in October 2026". Saved
with `create` `/blog/posts` (no `status`, so a draft), read back, and
reported with its ID and future URL. Publishing and the inbound link from
the "How to become" post waited for the user.

Sources: https://cavuno.com/docs/mcp/cookbook/create-and-publish-a-blog-post,
https://cavuno.com/blog/aeo-for-job-boards
