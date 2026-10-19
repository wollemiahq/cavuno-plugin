# Custom domain and DNS

## Contents

- Choose the hostname
- Add the domain
- DNS records
- Verify
- Make it primary
- Move to a new domain
- Stuck domain fixes
- Cavuno subdomain

## Choose the hostname

A subdomain such as `jobs.example.org` keeps an existing website intact and
uses a CNAME record. A root domain such as `examplejobs.com` uses an A
record. Google does not promise a ranking advantage for either. The board
keeps working on its Cavuno subdomain the whole time.

## Add the domain

`create` `/domains` with `{ "domain": "jobs.example.org" }`. Hostnames are
lowercased. The response has the domain `id`, `status`, and
`verificationInstructions` (`method: "txt"`, `name`, `expectedValue`) when a
TXT ownership record is needed.

"Domain already in use" means another Cavuno board has it; the owner must
contact support.

If the domain already serves the user's own site built with the
`@cavuno/board` SDK, it is a self-hosted domain: DNS stays with their host
and only the TXT record is added. That setup happens in the dashboard
(Settings → SDK); do not add it here as a Cavuno-hosted domain.

## DNS records

The API returns the TXT verification record. The CNAME target or A record
IP is shown in the dashboard: Settings → Domains → the domain's menu →
Manage. Send the user there for the exact values and always use what that
dialog shows now, not values from an old email or screenshot.

Tell the user to add, at their DNS provider:

| Hostname type | Record |
| --- | --- |
| Subdomain (`jobs.example.org`) | CNAME from `jobs` to the target shown in Manage |
| Root domain (`examplejobs.com`) | A record for `@` to the IP shown in Manage; optional CNAME for `www` |
| Any, when shown | TXT record with the exact `name` and `expectedValue` |

Also:

- TTL 3600, or Auto.
- Cloudflare: set the record to **DNS only** (grey cloud). The proxy makes
  verification fail.
- Turn off any registrar domain forwarding, URL forwarding or URL redirect
  on the same hostname.
- If the dialog offers Domain Connect, the user can apply the records in
  one click at a supported provider.

## Verify

1. After the user says the records are in, `create`
   `/domains/{id}/verify`. It answers 202 with an operation.
2. `read` `/operations/{id}` every few seconds until `state` is
   `succeeded`, `failed` or `cancelled`.
3. `read` `/domains/{id}` and report `status`, `statusReason` and
   `verifiedAt`.

Propagation usually takes minutes to a few hours and can take up to 48
hours. Do not loop: if it is still pending, tell the user to check back, or
to run `dnschecker.org` for the hostname. SSL is issued automatically after
verification and can take up to 15 minutes.

## Make it primary

Only an active domain can be primary. After confirmation, `update` `PATCH
/settings` `{ "primaryDomainId": "<domain id>" }`, then `read` `/settings`.
The primary domain is used for canonical URLs, job-alert links and job
structured data. Every other active domain, root and `www`, then
redirects permanently to it, keeping path and query string.

## Move to a new domain

1. Add the new domain and verify it as above. Keep the old one.
2. Make the new domain primary.
3. Keep the old domain connected in Cavuno with its DNS still pointing at
   Cavuno. That is what keeps old links and job URLs redirecting. A
   registrar redirect on the old domain breaks its HTTPS and shows a
   Mismatch status.
4. In Google Search Console: verify the new property, then use Change of
   address in the old property.
5. If the board uses its own Google or LinkedIn sign-in apps, add the new
   domain's callback URL to them.

Removing the old domain from Cavuno stops the redirect. Only `delete`
`/domains/{id}` when the user confirms they no longer need the old links to
work.

## Stuck domain fixes

| Symptom | Fix |
| --- | --- |
| Pending or Awaiting DNS | Compare each record with Manage exactly (type, host, value); check propagation; then verify again |
| Records look right, domain opens elsewhere | Remove registrar forwarding on that hostname |
| Mismatch | A record points elsewhere, often a registrar redirect; restore the Manage values |
| Certificate warning | Wait up to 15 minutes, retry in a private window |
| Pending more than 48 hours with correct records | Contact hi@cavuno.com with the domain and a screenshot of the DNS records |
| Google still shows the old address | The domain must be active and primary; then inspect the custom-domain URL in a matching Search Console property and resubmit the sitemap. Google sets the timing |

## Cavuno subdomain

The `*.cavuno.app` or `*.cavuno.com` address is changed in the dashboard:
Settings → Domains → the Cavuno row's menu → Change Cavuno domain. The board
name visitors see is a separate setting (`name`).

Sources: https://cavuno.com/docs/board-setup/connect-custom-domain,
https://cavuno.com/docs/board-setup/change-domain,
https://cavuno.com/docs/board-setup/change-cavuno-subdomain,
https://cavuno.com/docs/mcp/reference/domains,
https://cavuno.com/docs/troubleshooting
