# Example: a featured job plan

## Input

"Add a featured job option: $349, live for 45 days, shown as featured. Put
it on the pricing page."

## Plan

1. `read` `/billing/connect`: connected, accepting payments, payouts
   enabled.
2. `read` `/plans`: one public plan, "Standard post" (`one_time`, $199,
   30 days, no featured slot).
3. Show the table and the live impact, then ask for a yes:

| | Standard post (today) | Featured post (new) |
| --- | --- | --- |
| Kind / purpose | one_time / job_posting | one_time / job_posting |
| Price | USD 199.00 | USD 349.00 |
| Duration | 30 days | 45 days |
| Featured | no | 1 featured slot, automatic |
| Public | yes | no until tested, then yes |

Live impact: once public, employers can buy it at checkout on the live
Stripe account. Nothing changes for the Standard plan.

4. After the yes, each call with its own fresh `idempotencyKey`:
   `create` `/plans` with `name: "Featured post"`, `kind: "one_time"`,
   `purpose: "job_posting"`, `isPublic: false`; `update` PUT
   `/plans/{id}/price` with `currency: "usd"`, `amountCents: 34900`;
   `update` PUT `/plans/{id}/features` with `jobs.max_active` "1",
   `jobs.duration_days` "45", `jobs.featured_slots` "1",
   `jobs.feature_selection_mode` "auto". Read `/plans/{id}` back.
5. Offer the no-charge test: a 100% coupon (`duration: once`,
   `appliesToPlanIds: [id]`) and a promotion code `TESTFEATURED` with
   `maxRedemptions: 1`, created only after a separate yes.

## Output

"Created **Featured post** (`pln_8f2`, private): USD 349.00, 45 days, one
automatic featured slot, checked against the plan.

Before it goes public, I suggest a free test purchase. Shall I create a
100% off coupon for this plan with the one-use code TESTFEATURED? After the
test I will archive the code and, with your yes, make the plan public. That
puts it on live checkout immediately."
