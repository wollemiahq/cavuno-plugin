# Digital products

## Contents

- What a digital product is
- Create and list a product
- Product fields
- Files and links
- Sales, downloads and buyers
- Reviews
- Dashboard-only steps
- Webhooks

## What a digital product is

A file or link the board sells or gives away to job seekers or employers:
templates, reports, guides, question banks, a template to copy, a booking
link. Buyers pay on the board's own Stripe account (Cavuno takes no fee) and
get a download page plus an email with its link. Free products need no
Stripe connection.

Every digital product is a plan with `purpose: digital_product`. Its product
fields live under `digitalProduct` on `/plans` and `/plans/{id}`. The board's
website needs product pages to show it; the dashboard product drawer says
when there is no link to share yet.

## Create and list a product

1. `create` POST `/plans` with `name`, `description`, `purpose:
   "digital_product"`, `kind` (`free` for free pricing, `one_time`
   otherwise) and `digitalProduct.pricing`:
   - `{ "kind": "free" }`
   - `{ "kind": "fixed", "currency": "usd", "amountCents": 2900 }`
   - `{ "kind": "pay_what_you_want", "currency": "usd", "minimumCents": 500, "suggestedCents": 1500 }`
     (`minimumCents: 0` lets buyers pay nothing)
   Do not send `isPublic: true`, `billingInterval`, `pricingMode` or
   contact fields; they are refused for digital products.
2. Add at least one file or link (below) and wait until it is `ready`.
3. List it on the board: `update` PATCH `/plans/{id}` with `isPublic: true`
   (its own confirmation: it goes live). Unlisted products can still be
   bought by anyone with the product page link once they have a ready file
   or link.

`PUT /plans/{id}/price` (with an `idempotencyKey`) sets a fixed price. To
switch to free or pay what you want, PATCH `digitalProduct.pricing`.
Archive (`isArchived: true`) stops sales; past buyers keep their downloads.

## Product fields

`digitalProduct` on PATCH `/plans/{id}` (omitted fields stay, `null` clears):

| Field | Meaning |
| --- | --- |
| `slug` | Product page URL word; generated from the name when omitted |
| `summary` | One "You'll get…" line in the facts box |
| `details` | Up to 12 `{ label, value }` facts (60 / 200 characters) |
| `images` | Up to 8 media ids from POST `/media/upload`, cover first |
| `thankYouMessage` | On the download page and in the purchase email |
| `refundPolicy` | Shown at checkout, saved with each sale |
| `downloadCap` | Downloads per file per buyer, 1 to 1000 (default 5) |
| `stampPdfs` | Stamp the buyer's email and purchase date on every PDF page; turning it on stamps past sales too |
| `ratingsEnabled` | Buyers can rate and review (off by default) |
| `salesLimit` | Most sales (free claims count); `null` for no limit |
| `isEbook` | E-book tax code (reduced VAT in some countries) |
| `questions` | Up to 10 `{ id, kind: text/checkbox/dropdown, label, required, options }` asked before checkout |

EU right to cancel waiver: a required checkbox question, for example
`{ "id": "eu-waiver", "kind": "checkbox", "required": true, "label": "I want
immediate access and waive my 14-day right to cancel once the download
starts" }`. The operator is the seller and decides whether to use it; do not
present it as legal advice.

## Files and links

Up to 50 files and links per product; files up to 500 MB each. Allowed:
PDF, DOCX, XLSX, PPTX, CSV, TXT, EPUB, ZIP, PNG, JPEG, WebP, GIF, MP3, MP4
(type read from the bytes). Programs, HTML, SVG, XML, scripts,
macro-enabled or older Office files and unidentified files end `blocked`.
Every file is virus-scanned.

| Task | Operation |
| --- | --- |
| List files and states | `read` GET `/plans/{id}/files` |
| Add a link | `create` POST `/plans/{id}/files` (`displayName`, https `url`, `description`) |
| Rename, describe, move, disable, enable | `update` PATCH `/plans/{id}/files/{fileId}` (`displayName`, `description`, `position`, `disabled`) |
| Delete for everyone | `delete` `/plans/{id}/files/{fileId}` (own confirmation; past buyers lose it) |
| Upload or replace a file | POST `/plans/{id}/files/uploads`, PUT the bytes to the returned URLs, POST `.../uploads/{uploadId}/complete` |

The connector cannot send file bytes to the upload URLs. Ask the user to
upload files in the dashboard (Settings → Products → the product → Content →
Add files), or to run the upload from a script with their API key. Links
work through the connector.

States: `uploading`, `checking`, `ready` (delivered), `flagged`
(`flagReason` `threat` or `unscannable`; never delivered, the team is
emailed), `blocked` (`blockedReason`), `disabled` (kept for earlier buyers,
hidden from new ones). A listed product keeps at least one ready file or
link (409 `digital_product_last_deliverable`); unlist it first.

## Sales, downloads and buyers

| Task | Operation | Permission |
| --- | --- | --- |
| List a product's sales | `read` GET `/plans/{id}/purchases` | `billing.read` |
| Correct a mistyped buyer email | `update` PATCH `/plans/{id}/purchases/{purchaseId}` (`email`) | `plans.manage` + `billing.read` |
| Reset downloads | `create` POST `/plans/{id}/purchases/{purchaseId}/downloads/reset` (optional `fileId`) | `plans.manage` + `billing.read` |
| Resend the download link | `create` POST `/plans/{id}/purchases/{purchaseId}/resend` | `plans.manage` + `billing.read` |

Sales contain buyer emails and answers: report counts and totals unless the
user asks for contact details. `status` is `awaiting_payment`, `paid`,
`free` or `access_removed`; `transactionId` links a paid sale to
`/transactions` (null for free claims). Correcting the email keeps the same
link; resend afterwards if the buyer needs it.

## Reviews

`read` GET `/plans/{id}/reviews` (hidden ones included, newest first).
`update` PATCH `/plans/{id}/reviews/{reviewId}` with `reply` (public;
`null` removes it) or `hidden`. Show the user the reply text before posting.

## Dashboard-only steps

- Refund a sale, end or restore a buyer's access: the connector has no tools
  for these, so hand them to the user. In the dashboard: Transactions → filter
  by the product (Settings → Products → row menu → View sales) → row menu →
  Refund… / End access… / Restore access. Free claims have no refund, only
  End access… / Restore access. The same actions are in the API
  (`POST /v1/transactions/{id}/refunds`, `/remove-access`, `/restore-access`;
  for free claims `POST /v1/plans/{id}/purchases/{purchaseId}/remove-access`
  and `/restore-access`), which need `plans.manage` and `billing.read`.
- Edit the four buyer emails: Email editor → Digital products.

## Webhooks

`digital_product.purchased` and `digital_product.refunded` on webhook
endpoints (need `billing.read`). `refunded_amount_cents` is the current
refunded total; it can go down when a refund fails.

Sources: https://cavuno.com/docs/monetization/digital-products,
https://cavuno.com/docs/sdk/build-your-board/digital-products,
https://cavuno.com/docs/webhooks/events
