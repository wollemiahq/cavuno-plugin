# Monetization operations and dashboard hand-offs

## Contents

- Operation table
- Coupon and promotion code fields
- Dashboard-only steps
- Stripe payment connection
- Test a plan without being charged
- What lives only in Stripe

## Operation table

"Needs key" means the call fails with 400 `validation_bad_request` unless it
carries an `idempotencyKey` (the `Idempotency-Key` header). Send a fresh key
on every write anyway, as `cavuno-basics` says.

| Task | Operation | Needs key |
| --- | --- | --- |
| List or read plans | GET `/plans`, `/plans/{id}`, `/plans/{id}/features` | n/a |
| Create a plan (metadata) | POST `/plans` | No |
| Rename, publish, hide, reorder, archive a plan | PATCH `/plans/{id}` | No |
| Set plan features | PUT `/plans/{id}/features` | No |
| Set or change a plan price | PUT `/plans/{id}/price` (`currency`, `amountCents`) | Yes |
| Feature or unfeature one job | PATCH `/jobs/{id}` `isFeatured` | No |
| List or read coupons | GET `/coupons`, `/coupons/{id}` | n/a |
| Create a coupon | POST `/coupons` | Yes |
| Rename a coupon | PATCH `/coupons/{id}` | No |
| Delete a coupon | DELETE `/coupons/{id}` (deactivates its codes) | No |
| Create a promotion code | POST `/coupons/{id}/promotion-codes` | Yes |
| Archive a promotion code | POST `/coupons/promotion-codes/{id}/archive` | No |
| Read the paywall | GET `/paywall`, `/paywall/subscriptions` | n/a |
| Change the paywall | PUT `/paywall` (full replacement) | Yes |
| Sales-led plans (deprecated) | POST, PATCH, publish, hide, archive, reorder under `/sales-led-plans` | Yes; prefer a contact plan |
| Members-only posting | PATCH `/settings` `postingRequiresMembership` | No |
| Payment connection status | GET `/billing/connect` | n/a |
| Stripe onboarding link | POST `/billing/connect/onboarding-link` | No; a person completes it |
| Upgrade Cavuno's own plan | POST `/billing/upgrade` (`planKey`, `interval`) | Yes; returns a hosted URL for the person, never pay |

## Coupon and promotion code fields

- `POST /coupons`: `name` (up to 40 characters) and `duration` (`once`,
  `forever`, `repeating` with `durationInMonths`) are required; then
  `percentOff` (0 to 100) or `amountOff` (major units) with `currency`,
  `maxRedemptions`, `redeemBy` (Unix milliseconds) and `appliesToPlanIds`
  (cannot be changed later).
- `POST /coupons/{id}/promotion-codes`: `code` is required; also
  `maxRedemptions`, `expiresAt` (ISO 8601), `firstTimeTransaction`,
  `minimumAmount` with `minimumAmountCurrency`.
- After creating, `read` `/coupons/{id}` and check `percentOff` or
  `amountOff`, `duration` and `appliesTo`. Promotion codes cannot be read
  back; report the `id`, `code` and `active` from the create response.

## Dashboard-only steps

These have no API operation. Give the path, then each field with its value.

- Job seeker plan features (Free, Paid, Not included): Settings → Products →
  the job seeker product.
- Assign or revoke a membership: Companies → row menu → Manage membership.
- Tax: Settings → Products → Taxes (read-only; changes happen in Stripe).

## Stripe payment connection

`GET /billing/connect` returns `connected`, `acceptingPayments`,
`payoutsEnabled`, `detailsSubmitted`, `actionRequired`.

| State | Tell the user |
| --- | --- |
| `connected: false` | Paid products need Stripe. Offer an onboarding link. |
| Connected, `actionRequired: true` | Stripe needs more information; open the Stripe Dashboard account status, or use a new onboarding link. |
| `acceptingPayments` true, `payoutsEnabled` false | Do not launch paid plans yet; finish the payout requirement in Stripe. |
| All true | Ready for paid plans. |

The onboarding link (`url`, `expiresAt`) is single use and expires within
minutes. Show it once, ask the user to open it now, and request a new one if
it expired. Disconnecting Stripe is owner-only and dashboard-only.

## Test a plan without being charged

Cavuno has no test mode and employers can only buy public plans.

1. Create a coupon (`percentOff: 100`, `duration: once`,
   `appliesToPlanIds` = the plan) and a promotion code with
   `maxRedemptions` = the number of test purchases.
2. Make the plan public (its own confirmation), complete checkout on the
   public posting flow with the code.
3. Check the order, slot or credit count, duration, featured behavior and
   interval against the plan table.
4. For a subscription, cancel the test subscription in Stripe; a Once
   coupon covers only the first period.
5. Archive the promotion code (`create` `/coupons/promotion-codes/{id}/archive`)
   and make the plan private again if launch is later.

Use a partial discount instead when tax must be tested; a fully discounted
order has nothing to tax.

## What lives only in Stripe

Refunds, cancelling an employer subscription, payment retries for failed
renewals, bank-transfer payment methods, Stripe Tax registrations, and
revenue reports such as MRR and churn (Stripe Reports → Revenue).

Sources: https://cavuno.com/docs/monetization/connect-stripe,
https://cavuno.com/docs/monetization/create-pricing-plans,
https://cavuno.com/docs/monetization/discount-codes,
https://cavuno.com/docs/monetization/job-seeker-paywall,
https://cavuno.com/docs/monetization/tax-collection,
https://cavuno.com/docs/monetization/employer-subscriptions,
https://cavuno.com/docs/monetization/invoice-billing,
https://cavuno.com/docs/mcp
