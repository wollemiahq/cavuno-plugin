# Revenue reporting

## Where the numbers are

| Question | Read | Notes |
| --- | --- | --- |
| Payments and invoices in a period | `/transactions` | Newest first by `date`. Filters: `status` (`paid`, `open`, `void`, `uncollectible`), `kind` (`card_post`, `invoice`, `subscription`, `service`), `search`. No date filter: page with `cursor` until rows are older than the start date. |
| One transaction | `/transactions/{id}` | `hostedInvoiceUrl`, `receiptUrl` for the user to open. |
| Recurring employer customers | `/employer-subscriptions` | Filters: `status`, `companyId`. Fields: `planName`, `companyName`, `status`, `currentPeriodEnd`, `cancelAtPeriodEnd`. |
| One digital product's sales and free claims | `/plans/{id}/purchases` | Paid sales also appear on `/transactions` (`transactionId`); free claims only here. Contains emails; report counts. |
| Candidate paywall subscribers | `/paywall/subscriptions` | Filters: `status`, `kind` (`recurring`, `lifetime`). Contains emails; report counts. |
| Revenue next to traffic | `/analytics/overview` | `stripe_revenue` metric when available; see the `review-performance` skill. |

## Compute totals

1. Fix the range in the user's terms ("September" = the 1st to the 30th)
   and state it.
2. Count `status: paid` rows dated in the range. Report `open` separately
   (billed, not collected). Leave out `void`; list `uncollectible` as
   written off.
3. `amountCents` is in minor units: divide by 100 for most currencies; for
   zero-decimal currencies such as JPY and KRW the amount is already whole.
4. Total each `currency` separately. Never add USD to EUR.
5. Split by `kind` when useful: card posts, invoices, subscription renewals,
   services.
6. Past due is not a stored status: an `open` row whose `invoiceDueAt` is
   before today is past due. The docs say unpaid invoices are swept (voided)
   on their due date, so flag open invoices due soon for the user to chase.

Report shape: one line per currency (paid total, transaction count), an
"open invoices" list (description, amount, due date, past due or not), and
the excluded rows by status. Use `customerLabel` (company name) rather than
`customerEmail` unless the user asks for contact details.

## Measures worth reporting

From the docs: revenue by product, transaction count, average order value,
renewal or repeat-purchase rate, refunds, payment failures. For
subscriptions: active count, `cancelAtPeriodEnd` (canceling), past due.
Cavuno publishes no revenue benchmark by stage; do not supply one.

Monthly recurring revenue, churn and revenue by plan are in the Stripe
Dashboard under Reports → Revenue. Refunds, voiding and resending invoices
happen in the dashboard (Transactions row menu, owners and admins) or Stripe.
Refunds also have API routes (`POST /v1/transactions/{id}/refunds`), but the
connector has no refund tools, so hand them to the user.

## Pitfalls

- A page of 100 rows is not the whole period; say when you stopped early.
- A failed read is not "no revenue"; report the error code.
- Subscription renewals appear as their own transaction rows.

Sources: https://cavuno.com/docs/monetization/transactions-ledger,
https://cavuno.com/docs/monetization/employer-subscriptions,
https://cavuno.com/docs/monetization/monetization-overview,
https://cavuno.com/docs/monetization/invoice-billing,
https://cavuno.com/docs/monetization/create-pricing-plans
