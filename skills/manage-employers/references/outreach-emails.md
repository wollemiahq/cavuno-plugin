# Employer outreach emails

## Contents

- Pick prospects
- Figures you may quote
- Templates
- Cadence and handoff

## Pick prospects

Companies whose jobs are already on the board (often through backfill or
imports) are warm leads: their roles are getting attention they did not
pay for.

1. `read` `/companies` page by page (`limit: 100`); keep companies with
   `publishedJobCount` above 0.
2. `read` `/employers/memberships` with `status: "approved"`; drop
   companies that already have employer members, since they post directly.
3. `read` `/analytics/job-visitors` for the period (`start`, `end` as
   YYYY-MM-DD; page through `nextCursor`) and sum `visitors` by
   `companySlug`. `read` `/analytics/apply-clicks` for the same period.
4. Rank by visitors, then by number of open roles. Show the user the top
   ten with their figures and let them choose whom to contact.

Several open roles means more posting value; staffing firms and recruiters
in the niche post often too. The user decides who fits their offer.

## Figures you may quote

| Figure | Source | Wording |
| --- | --- | --- |
| Visitors to a company's job pages | `/analytics/job-visitors` | "<n> people viewed your <title> listing between <start> and <end>" (unique sessions, not page views). |
| Apply clicks | `/analytics/apply-clicks` | "<n> candidates clicked through to apply" (clicks, not completed applications). |
| Board sessions and page views | `/analytics/overview` (last 30 completed days by default) | "<n> visits to the board in the last 30 days". |
| Job alert subscribers | `/subscribers/count` | "<n> job seekers get our job alerts". |
| Open roles at the company | `/companies/{id}` `publishedJobCount` | "<n> of your roles are listed". |

Quote only figures returned for the stated period. Write
`[price]`, `[featured option]`, `[contact name]` for anything else. Never add
industry statistics or claims the user has not given you.

## Templates

**Their jobs are already on the board**

```text
Subject: Your <job title> listing on <board name>: <n> visitors in <period>

Hi [contact name],

<Company>'s <job title> role is listed on <board name>, the job board for
<niche>. Between <start> and <end>, <n> people viewed it and <m> clicked
through to apply.

If you post directly, you can [featured option or direct-post benefit],
and we include it in the job alert we send to <subscribers> <niche> job
seekers. [Price or offer.]

Would you like me to set that up for your next role?

[Sender name], <board name>
```

**No listing yet (cold)**

```text
Subject: <Company> + <board name>: reaching <niche> candidates

Hi [contact name],

I saw you are hiring a <specific open role>. <Board name> reaches people
looking for <niche> roles: <sessions> visits in the last 30 days and
<subscribers> job alert subscribers.

[Offer, e.g. a free first listing that we set up for you.]

Worth a try for this role?
```

**Next email (3 to 5 days later)**

```text
Subject: Re: <original subject>

Hi [contact name], a quick note on featuring your <job title> role.
[One new fact from Cavuno data since the last email.] If it helps, reply
"yes" and I will set it up.
```

Keep each email short, reference a specific role, and lead with what the
employer already received.

## Cadence and handoff

- Suggested sequence: first email, then 3 to 5 days, then 7 to 10 days,
  then a last note at 14 days; after that, a quarterly check-in.
- Hand the drafts to the user in the chat with the figures' date range.
  The user sends them from their own email. Do not send email, and create
  drafts in a connected mail tool only when the user asks for that.

Sources: https://cavuno.com/blog/attract-employers-job-board,
https://cavuno.com/docs/backfill/what-is-backfill, https://cavuno.com/docs/api
