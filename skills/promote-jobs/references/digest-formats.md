# Digest and social post formats

## Contents

- Copy rules for every channel
- UTM scheme
- Weekly newsletter or digest
- LinkedIn
- X
- Facebook and community groups
- Fields to pull from each job

## Copy rules for every channel

- Lead with the role, not the board: title, company, where (remote option
  or city), salary when the record has it. Specifics earn clicks; "We're
  hiring!" does not.
- Show salary exactly as stored (`salaryMin`, `salaryMax`,
  `salaryCurrency`, `salaryTimeframe`). Missing salary means no salary line.
- One hook per job, taken from the description (the problem, the team, a
  stated benefit). No adjectives the record does not support.
- Featured jobs may be marked "Featured"; do not imply endorsement.
- Plain US English, sentence case, no emoji unless the user asks.
- End with one call to action pointing to the board's jobs page or the
  alert signup, also tagged.

## UTM scheme

Cavuno classifies each visit once from its first page: a `utm_medium` of
`email` becomes the Email channel and `social` becomes Organic social, and
`GET /analytics/traffic` reports visits and apply clicks per
`utm_campaign`, `utm_source` and `utm_medium` (with `campaignGroupBy=content`,
also per `utm_content` and `utm_term`).

| Parameter | Value |
| --- | --- |
| `utm_source` | `newsletter`, `linkedin`, `x`, `facebook`, or the group's name |
| `utm_medium` | `email` for newsletters, `social` for organic posts |
| `utm_campaign` | `weekly-jobs-YYYY-MM-DD` (the send or post date) |
| `utm_content` | the job slug, so each job's clicks can be compared |

Append with `?` or `&` depending on whether the URL already has a query.
Keep the same campaign name across channels for one week so they can be
compared.

## Weekly newsletter or digest

- Subject: the count and the niche, for example "8 new climate policy jobs
  this week". Preview text: two standout roles.
- Intro: two to four sentences: what is new this week, one notable
  employer or trend visible in the jobs.
- Job list, one block per job:
  **Title** at Company · Remote or City · Salary (if any)
  One-line hook.
  Link.
- Featured jobs first, then newest first.
- Footer: link to all jobs and to the job alert signup.
- Keep it short; the email-marketing post suggests under 500 words for a
  weekly newsletter.

Cavuno already emails a weekly alert digest to each confirmed subscriber
for their saved filters. A newsletter is the operator's own send from their
email tool; say so when handing over the draft.

## LinkedIn

- Post from a personal profile when possible; the social-media post says
  personal posts reach more people than company pages.
- Format that works for operators: a "Top 5 [niche] jobs this week" roundup
  with salary ranges, one line per job, then the link.
- Test the link in the post against a link in the first comment; the post
  says not to assume either wins.

## X

- One post per job: title, salary, remote or location, company, link.
  Stay within 280 characters including the link.
- Optional: one hashtag the niche actually searches (for example
  `#[niche]jobs`).

## Facebook and community groups

- Check the group's rules on job posts first; give the user the draft and
  that reminder.
- A short roundup works better than one post per job in groups.

## Fields to pull from each job

From `GET /jobs/{id}`: `title`, `company` (name), `remoteOption`,
`officeLocations`, `salaryMin`, `salaryMax`, `salaryCurrency`,
`salaryTimeframe`, `employmentType`, `isFeatured`, `publishedAt`,
`expiresAt`, `description` (for the hook) and `links.public`.

Sources: https://cavuno.com/docs/analytics/dashboard-analytics,
https://cavuno.com/docs/job-alerts/email-notifications,
https://cavuno.com/blog/email-marketing-for-job-boards,
https://cavuno.com/blog/social-media-marketing-for-job-boards,
https://cavuno.com/blog/job-board-marketing
