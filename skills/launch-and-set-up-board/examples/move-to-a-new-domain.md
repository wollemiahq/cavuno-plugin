# Example: move the board to a new domain

## Input

"We rebranded. Move the board from jobs.oldname.example to
careers.newname.example."

## Plan

1. `read` `/domains`: `jobs.oldname.example` is active and primary
   (`dom_old`). No row for the new hostname.
2. Show the plan and ask for a yes:
   - Add `careers.newname.example`. Nothing changes for visitors yet.
   - You add the DNS records; I check verification.
   - Once it is active, I make it primary (a separate yes).
   - The old domain stays connected so every old link redirects.
3. After the yes: `create` `/domains` `{ "domain": "careers.newname.example" }`
   returns `dom_new`, `status` pending, and a TXT record.

## Output to the user

> Added careers.newname.example. At your DNS provider for newname.example:
>
> 1. Add a CNAME for `careers` pointing to the target shown in
>    Settings → Domains → careers.newname.example → Manage.
> 2. Add the TXT record with the exact name and value Cavuno returned
>    (also shown in that Manage dialog).
> 3. If you use Cloudflare, set both to DNS only (grey cloud).
>
> Tell me when they're in and I'll run the check. Your board stays on
> jobs.oldname.example until then.

## Later in the conversation

- User: "Done." → `create` `/domains/dom_new/verify` (202, operation
  `op_51`) → poll `/operations/op_51` until `succeeded` → `read`
  `/domains/dom_new`: active.
- Ask: "Make careers.newname.example primary? Old links will redirect to
  it." → yes → `update` `PATCH /settings` `{ "primaryDomainId": "dom_new" }`
  → `read` `/settings`: `primaryDomainId` is `dom_new`.
- Report: new domain active and primary; old domain still connected and
  redirecting; next, verify the new site in Search Console and use Change
  of address from the old property. Do not delete the old domain.
