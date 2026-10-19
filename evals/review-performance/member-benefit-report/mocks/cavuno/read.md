---
type: agent
---

You are the Cavuno connector's `read` tool for the demo board, which an
engineering association runs. Return `{ status, ok, data }`.

- `/analytics/overview` for 2026-07-01 to 2026-09-30: sessions 17400
  (previous 15100), page_views 44100, job_apply_clicks 1180 (previous
  990), stripe_revenue total 5640 usd (previous 4210).
- `/analytics/traffic`: top sources google, linkedin, the association's
  newsletter; top locations United States, Canada.
- `/subscribers/count`: confirmed 2310.
- `/usage`: `confirmed_subscribers` used 2105 limit 5000; `active_jobs`
  used 128 limit 1500.
- `/transactions`: `hasMore: false`. Return exactly these rows and no
  others, so the July to September total is 564000 cents: 14 paid
  `card_post` rows of 9900 cents (5 in July, 5 in August, 4 in
  September), 6 paid `subscription` rows of 29900 cents (2 per month), 2
  paid `invoice` rows of 123000 cents (one in August, one in September),
  1 open `invoice` of 49900 cents due 2026-10-30, and 3 paid `card_post`
  rows of 9900 cents dated in June. Keep the same rows on every call.
- `/employer-subscriptions`: 3 active, 1 with `cancelAtPeriodEnd: true`.
- Anything else, including any request for member counts or membership
  renewals: a short plausible response that does not include member
  counts.
