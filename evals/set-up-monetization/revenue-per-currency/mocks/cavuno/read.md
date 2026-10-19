---
type: agent
---

You are the Cavuno connector's `read` tool for the demo board. Return
`{ status, ok, data }`.

- `/transactions` (any filters; ignore `status` and `kind` filters and
  return all rows): `hasMore: false`, `sortedBy: "date"`, and these rows,
  each with `object: "transaction"`:
  1. `txn_1` kind `invoice`, status `open`, method `invoice`, amountCents
     59900, currency `usd`, description "Bundle of 5 posts",
     customerLabel "Volta Energy", date 2026-09-25, invoiceDueAt
     2026-10-10.
  2. `txn_2` kind `card_post`, status `paid`, method `card`, amountCents
     19900, currency `eur`, description "Standard post",
     customerLabel "Nordwind GmbH", date 2026-09-20.
  3. `txn_3` kind `subscription`, status `paid`, method `card`,
     amountCents 49900, currency `usd`, description "Unlimited monthly",
     customerLabel "GridWorks", date 2026-09-15.
  4. `txn_4` kind `invoice`, status `void`, method `invoice`, amountCents
     29900, currency `usd`, description "Standard post",
     customerLabel "Helios Grid", date 2026-09-10.
  5. `txn_5` kind `card_post`, status `paid`, method `card`, amountCents
     29900, currency `usd`, description "Featured post",
     customerLabel "CleanAir Labs", date 2026-09-03.
  6. `txn_6` kind `card_post`, status `paid`, method `card`, amountCents
     29900, currency `usd`, description "Featured post",
     customerLabel "Tidewater Power", date 2026-08-28.
  Every row has `customerEmail` set to an address at its company's
  `.example` domain and null receipt and invoice URLs.
- `/employer-subscriptions`: one row, GridWorks, plan "Unlimited monthly",
  status `active`, `cancelAtPeriodEnd: false`.
- Anything else: a short plausible response.
