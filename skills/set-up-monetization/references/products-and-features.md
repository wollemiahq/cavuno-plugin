# Products, plan features and featured jobs

## Contents

- Pick the product type
- Plan fields
- Feature keys and values
- Featured jobs
- Memberships and member pricing
- Contact-led and service plans
- Job seeker paywall
- Common requests mapped to plans

## Pick the product type

Every product is a plan (`/plans`). `purpose` says what it entitles.

| The user wants to sell | `purpose` | Typical `kind` |
| --- | --- | --- |
| Job posts, packs of posts, posting subscriptions | `job_posting` | `one_time`, `bundle`, `subscription`, `free` |
| Resume unlocks or candidate contact credits | `talent_access` | `subscription` or `one_time` |
| Company membership (member rate, included posts) | `membership` | `free`, `one_time`, `subscription` |
| Paid access for job seekers | `job_seeker` | `subscription` |
| A service with no posting or access entitlement (retainer, branding package) | `employer_service` or `job_seeker_service` | any, usually with `pricingMode: contact` |
| A downloadable file or link (template, report, guide, booking link) | `digital_product` | `free` or `one_time`; see [digital-products.md](digital-products.md) |

`kind` meanings (from the docs):

- `free`: post without paying (launch offers, community boards, or a
  temporary plan while invoicing outside Cavuno).
- `one_time`: one fee for one post.
- `bundle`: a pack of one-time posts sold as credits. Publishing spends a
  credit; it does not come back when the job closes. Repeat purchases add up.
- `subscription`: recurring, for a number of jobs live at once (slots) or
  unlimited. A slot frees when the job expires or closes.

`kind`, `purpose`, `billingInterval` and `billingIntervalCount` are set at
creation only; PATCH cannot change them. To change one, create a new plan and
archive the old one.

## Plan fields

`POST /plans` body: `name` and `kind` are required; also `description`,
`billingInterval` (`day`, `week`, `month`, `year`; required for
`subscription`), `billingIntervalCount` (quarterly is `month` with 3),
`purpose`, `isPublic` (defaults to false), `isRecommended`, `displayOrder`,
`pricingMode` (`priced` or `contact`), and for contact plans `priceText`,
`ctaText`, `ctaDestination` (https, `mailto:` or `tel:` URL).

`POST /plans` creates metadata only. The price is set separately with
`PUT /plans/{id}/price` (`currency`, `amountCents` in minor units), which
needs a ready payment connection and an `idempotencyKey`.

`PATCH /plans/{id}` changes `name`, `description`, `isPublic`,
`isRecommended`, `isArchived`, `displayOrder`, `pricingMode`, `priceText`,
`ctaText`, `ctaDestination`. An empty string clears a contact field.

## Feature keys and values

`PUT /plans/{id}/features` replaces the whole set: `{ features: [{ key, value }] }`.
A key you leave out is removed. Unknown keys and invalid values are refused
with 400. The spec types `value` as a string; read an existing plan's
features to see the stored form before writing.

| Key | Meaning | Value | Used on |
| --- | --- | --- | --- |
| `jobs.max_active` | Posts the plan sells: bundle credits, or subscription slots | integer, or `unlimited` | job posting, membership |
| `jobs.duration_days` | Days each job stays live (docs default 30) | integer | job posting, membership |
| `jobs.featured_slots` | How many of those posts can be featured | integer, or `unlimited` (all) | job posting, membership |
| `jobs.feature_selection_mode` | Who picks featured jobs | `auto` or `manual` | job posting, membership |
| `jobs.included_posts` | Member post credits per period | integer, or `unlimited` | membership only |
| `jobs.included_featured` | Member featured credits | integer, or `unlimited` | membership only |
| `jobs.posting_discount_percent` | Member discount at checkout | 0 to 100 | membership |
| `talent.profile_unlocks` | Redacted profiles an employer can reveal | integer, or `unlimited` | talent access, membership |
| `talent.messages_sent` | Candidates an employer can contact | integer, or `unlimited` | talent access, membership |

Rules: featured slots never exceed the posts the plan sells. Service plans
take no system features. Job seeker plan features (listings, matches, alerts
as Free, Paid or Not included) are set in the dashboard product editor.

## Featured jobs

- Featured jobs get a Featured badge and highlighted card, sort above
  non-featured jobs, and get a boost in keyword and semantic search.
- Sell featured placement through plan features: `jobs.featured_slots`
  plus `jobs.feature_selection_mode`. `auto` features each new post while
  slots remain, then posts the rest as standard; `manual` shows the
  employer a "Featured job" checkbox while slots remain.
- Feature one job by hand: `update` PATCH `/jobs/{id}` with
  `isFeatured: true`. This does not use plan slots. Read the job back and
  check `isFeatured`.
- Examples from the docs: one featured post = one-time plan, 1 featured
  slot, auto. Bundle with every post featured = equal posts and featured
  slots, auto.

## Memberships and member pricing

- Use a membership, not a coupon, when membership is why a company pays a
  different rate. The discount and included posts apply automatically at
  checkout; codes can leak and make members type a code.
- Create the level with `purpose: membership`. Keep it private to only
  assign it; make it public (with a price) to sell it on the board.
- Assigning or revoking a membership for one company is dashboard-only:
  Companies → row menu → Manage membership.
- Members-only posting: PATCH `/settings` `postingRequiresMembership: true`
  (a public change: confirm separately). It gates posting, not viewing.
- Only public membership products are named on company profiles.

## Contact-led and service plans

- For "talk to sales", "from $2,000" or a percentage fee, create a plan with
  `purpose: employer_service` (or `job_seeker_service`), `pricingMode:
  contact`, `priceText`, `ctaText`, `ctaDestination`. No Stripe charge is
  created, and this works through the connector.
- `/sales-led-plans` is deprecated; do not create new ones. Its writes also
  require an `idempotencyKey`.

## Job seeker paywall

- `GET /paywall`: `enabled`, `previewCount` (1 to 500 free jobs), lock-screen
  copy (`lockHeading`, `lockDescription` with `{{count}}`, `buttonText`,
  `disclaimerText`, `perMonthLabel`, `savingsTemplate` with `{{percent}}`),
  `currency` and six fixed offers: `daily`, `weekly`, `monthly`,
  `quarterly`, `yearly`, `lifetime`.
- `PUT /paywall` replaces all of it and needs an `idempotencyKey`. Enabling
  needs candidate profiles, a ready payment connection and at least one
  enabled offer above zero. Disabling ends active recurring subscriptions at
  period end.
- The docs also describe the paywall as on whenever a public job seeker
  product exists, and off once every one is unpublished or archived. Read
  both `/paywall` and the `job_seeker` plans before describing its state.

## Common requests mapped to plans

| Request | Plan |
| --- | --- |
| "$299 single post, 30 days" | `job_posting`, `one_time`; `jobs.max_active` 1, `jobs.duration_days` 30; price `amountCents` 29900 |
| "Featured upgrade" | separate `one_time` plan with `jobs.featured_slots` 1, mode `auto` |
| "5-pack, 2 featured" | `bundle`; `jobs.max_active` 5, `jobs.featured_slots` 2 |
| "Unlimited monthly posting" | `subscription`, `billingInterval` month; `jobs.max_active` `unlimited` |
| "Members post free, 10 a year" | `membership`; `jobs.included_posts` 10; private if assigned only |
| "Members get 20% off" | `membership`; `jobs.posting_discount_percent` 20 |

Sources: https://cavuno.com/docs/monetization/monetization-overview,
https://cavuno.com/docs/monetization/create-pricing-plans,
https://cavuno.com/docs/monetization/featured-jobs,
https://cavuno.com/docs/monetization/memberships,
https://cavuno.com/docs/monetization/service-products,
https://cavuno.com/docs/monetization/job-seeker-paywall,
https://cavuno.com/docs/monetization/talent-access-paywall,
https://cavuno.com/docs/monetization/invoice-billing
