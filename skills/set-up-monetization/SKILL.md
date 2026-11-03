---
name: set-up-monetization
description: Plans and changes how a Cavuno job board earns money, covering employer plans and their features (post limits, duration, featured slots), featured jobs, member pricing, contact-led service plans, digital products (downloadable files and links, their sales and reviews), discount coupons and promotion codes, the job seeker paywall and the Stripe payment connection, and reports revenue from transactions and employer subscriptions. Shows a before/after of every change and flags anything that affects live checkout. Use when the user says "set up pricing for our job board", "add a $299 featured job plan", "create a 20% launch discount code", "turn off the job seeker paywall", "give member companies a discount", "sell our salary report as a $29 download", "resend Ada her download link", "is Stripe connected?" or "how much did we make last month?".
---

# Set up monetization

Follow the `cavuno-basics` skill for tool use, confirmations and read-back.

## When to use / not

Use for anything an employer or job seeker pays for: plans, plan features,
prices, featured placement, coupons, promotion codes, the paywall, membership
pricing, service plans, digital products, payment readiness and revenue figures.

Not for: Cavuno's own subscription for the board (Settings → Billing; plan
facts at `/billing/subscription`), traffic or apply-click reports
(`review-performance`), or promoting jobs to subscribers (`promote-jobs`).

## Steps

1. **Check payment readiness.** `read` `/billing/connect`. Paid plans,
   prices, coupons and the paywall need `connected`, `acceptingPayments` and
   `payoutsEnabled` all true. If not, offer an onboarding link:
   `create` `/billing/connect/onboarding-link` (after a yes). Give the `url`
   to the user at once (single use, expires within minutes, a person must
   finish Stripe's form), then `read` `/billing/connect` again when they say
   they are done. A 409 `sandbox_connect_managed` means a sandbox board that
   needs no onboarding.
2. **Read the current catalog.** `read` `/plans`, then
   `/plans/{id}/features` for each plan you will touch. For discounts also `read` `/coupons`; for the paywall `read` `/paywall` and
   `/paywall/subscriptions` (summarize by status, never list emails).
3. **Map the request to products** with
   [references/products-and-features.md](references/products-and-features.md):
   `kind`, `purpose`, `pricingMode`, feature keys and values. `kind`,
   `purpose` and billing interval cannot be changed after creation, so get
   them right first. For a downloadable file or link (template, report,
   guide, booking link) follow
   [references/digital-products.md](references/digital-products.md): its
   price and product fields go in `digitalProduct`, and its files and sales
   have their own operations.
4. **Write the plan as a table** with one row per product: name, kind,
   purpose, price (currency and amount), features in plain words ("5 jobs
   live at once, 2 featured, 30 days"), public or private, and what changes
   compared with today. For coupons: percent or amount, duration, eligible
   plans, code, limits and expiry.
5. **Flag live checkout impact** in the same message. Cavuno has no test
   mode; every checkout runs on the live Stripe account, and only public
   plans can be bought. Say which changes go live the moment they are saved
   (making a plan public, archiving, a new price, a coupon on all plans,
   paywall on or off).
6. **Get a clear yes**, then write in this order: `create` `/plans`
   (metadata, `isPublic: false`), `update` PUT `/plans/{id}/price`
   (`currency`, `amountCents`), `update` PUT `/plans/{id}/features` (full
   replacement: send every key the plan should keep), then `update` PATCH
   `/plans/{id}` for visibility, order, archive or contact-plan fields.
   Coupons: `create` `/coupons`, then `create`
   `/coupons/{id}/promotion-codes` for each code.
7. **Send an `idempotencyKey`** on every write. Prices, coupons, promotion
   codes, `PUT /paywall` and sales-led plan writes fail without one (see
   `cavuno-basics`). A few steps stay in the dashboard for other reasons
   (assigning a membership, job seeker plan features, tax); give the exact
   values and path from
   [references/connector-handoffs.md](references/connector-handoffs.md).
8. **Recommend a no-charge test** before a plan goes public: a 100% coupon
   restricted to that plan, duration once, plus a promotion code limited to
   the number of test purchases; archive the code afterwards.
9. **Read back** every plan (`/plans/{id}` includes `price` and
   `features`) and coupon (`/coupons/{id}`) you changed and compare with the
   table. Promotion codes have no read operation; check the create
   response. Report IDs, what still waits on the dashboard, and the test
   steps.
10. **For revenue questions**, follow
    [references/revenue-reporting.md](references/revenue-reporting.md):
    `read` `/transactions` and `/employer-subscriptions`, total per currency,
    and say which rows were excluded.

Done when:

- [ ] `/billing/connect` state was reported before any paid change.
- [ ] The user saw a per-product table and the live checkout impact, and said yes.
- [ ] Every connector write was read back and matches the table.
- [ ] Every write carried its own `idempotencyKey`.
- [ ] Every dashboard-only step is listed with exact values and its path.
- [ ] Revenue figures are per currency, in major units, with the date range and excluded statuses stated.

## Guardrails

- New plans start private (`isPublic: false`). Making a plan public, a price
  change and an archive each get their own confirmation.
- Plans cannot be deleted; `isArchived: true` stops new purchases while
  existing buyers keep what they bought until their period ends.
- A price change only affects future purchases. Say so when an existing
  plan's price changes.
- Coupon plan restrictions cannot be edited later (PATCH only renames).
  Deleting a coupon deactivates all its promotion codes. Coupon
  `amountOff` is in major units (10 = $10); plan prices are in minor units
  (`amountCents`).
- Featured slots cannot exceed the plan's job posts. Manually featuring a
  job (`isFeatured: true` on PATCH `/jobs/{id}`) does not use plan slots.
- Turning the paywall off ends recurring candidate subscriptions at the end
  of their paid period; lifetime access is unaffected. State this before
  asking to confirm. `PUT /paywall` replaces the whole configuration: send
  everything `GET /paywall` returned, changing only what the user asked
  for (for example `enabled: false`). The docs also describe the paywall as off once every
  public `job_seeker` plan is unpublished or archived; read both after.
- Prices are hypotheses. Do not quote market rates as fact; if the user asks
  for a starting point, use [references/pricing-guidance.md](references/pricing-guidance.md)
  and attribute any figure to its source.
- Stripe Connect details, tax (Stripe Tax), refunds, cancellations and ad
  settings live in the Stripe or Cavuno dashboard; never complete a payment.
- A digital product starts unlisted; listing it, deleting a file (past
  buyers lose it) and turning on PDF stamping for a product with sales each
  get their own confirmation. The connector cannot upload file bytes: hand
  file uploads to the dashboard.

## References

- [references/products-and-features.md](references/products-and-features.md): plan kinds, purposes, feature keys and values, featured jobs, memberships, services.
- [references/digital-products.md](references/digital-products.md): digital product fields, files and links, sales actions, reviews and webhooks.
- [references/connector-handoffs.md](references/connector-handoffs.md): every monetization operation, which need an idempotency key, dashboard-only steps, Stripe connection, no-charge testing.
- [references/revenue-reporting.md](references/revenue-reporting.md): transactions, subscriptions, totals and past-due invoices.
- [references/pricing-guidance.md](references/pricing-guidance.md): how to choose, test and change prices, with sourced figures only.
- [examples/featured-plan.md](examples/featured-plan.md): a featured job plan from request to read-back.
