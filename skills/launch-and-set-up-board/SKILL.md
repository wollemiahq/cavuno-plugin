---
name: launch-and-set-up-board
description: Guides a Cavuno job board operator through launch and setup - a launch checklist checked against the live board, board settings changed with a before/after diff, connecting and verifying a custom domain with the exact DNS steps, job form fields and custom fields, 301 redirects from an old site, turning password protection on or off, and seeding an empty board with its first jobs. Use when the user says "help me launch my job board", "connect jobs.example.org", "my domain is stuck on pending", "take the password off the board", "add a security clearance field to the job form", "redirect our old /careers links" or "my board has no jobs yet".
---

# Launch and set up a board

Follow the `cavuno-basics` skill for tool use, confirmations and read-back.

## When to use / not

Use this skill for first launch, a relaunch or a single setup change.

- Health check only, nothing to change: `connect-and-check-board`
- Categories, skills, profile fields and collections: `organize-taxonomy-and-fields`
- Filling the board with jobs: `source-jobs` (backfill), `post-jobs` (by hand),
  `migrate-from-another-platform` (moving from another board)
- Employer pricing and Stripe: `set-up-monetization`
- Indexing toggles and team invites after launch: `tidy-board`

Design, pages, navigation, logos and the coming-soon page are made in the AI
website builder in the dashboard, not through the connector.

## Steps

1. **Baseline.** `read` `/me` (setup needs `owner` or `admin`; a `member`
   cannot change settings or domains), `/settings`, `/domains`, `/usage` and
   `/jobs` with `{ status: "published", limit: 100 }` to count live jobs.
2. **Pick the track** from what the user asked. For "help me launch", walk
   [references/launch-checklist.md](references/launch-checklist.md) and mark
   each item done, missing or "check in the dashboard" from the baseline
   reads. Then run the tracks below for the missing items, one at a time.
3. **Settings change.** Find the field with `search` on `PATCH /settings`.
   Show a table: setting, current value (from `/settings`), new value, what
   visitors will notice. Settings marked high-impact in
   [references/settings-changes.md](references/settings-changes.md) get
   their own confirmation. After the yes, `update` `PATCH /settings` with
   only the changed keys, then `read` `/settings` and compare.
4. **Custom domain.** Follow
   [references/domain-and-dns.md](references/domain-and-dns.md): add with
   `create` `/domains` `{ domain }`, give the DNS steps, start a check with
   `create` `/domains/{id}/verify` (returns 202 and an operation), poll
   `/operations/{id}`, then `read` `/domains/{id}`. Make it primary
   (`PATCH /settings` `{ primaryDomainId }`) only once it is active, and only
   after its own confirmation.
5. **Job form.** `read` `/settings/job-form`. To add, edit or remove a
   custom field, build the complete new `customFields` array (every
   existing field plus the change), show it, then `update` `PUT
   /settings/job-form/custom-fields`. Field order, shown and required use
   `GET` / `PUT /settings/form-layouts`. Rules and limits:
   [references/job-form-and-redirects.md](references/job-form-and-redirects.md).
6. **Redirects.** `read` `/redirects` to find existing rules. Map old paths to
   live destinations, show the table, then `create` `/redirects`
   `{ fromPath, toPath }` for one rule or `/redirects/batch` for up to 100.
   Check each batch entry.
7. **Password protection.** To turn it on: `create`
   `/settings/password-protection` `{ password }` (at least 8 characters).
   To turn it off: run the launch-day checks in the checklist first, show
   what is still missing, then after a yes `delete`
   `/settings/password-protection`. A 403 here means the plan does not
   allow it (Free plan boards stay protected). Read `/settings` back and
   confirm `config.passwordProtectionEnabled`.
8. **Empty board.** If live jobs are below the 20 to 50 the launch checklist
   recommends, say how many there are, check `active_jobs` remaining in
   `/usage`, and hand off to `source-jobs`, `post-jobs` or
   `migrate-from-another-platform` depending on where the jobs come from.
9. **Report.** List each change with before and after values, anything left
   for the dashboard, and the signed-out checks for the user to run (open
   the board in a private window; open one job and its apply path).

Done when:

- [ ] Every write was shown as a before/after (or a full array for custom
      fields) and confirmed before it ran.
- [ ] Every write was read back and matches the plan.
- [ ] No working domain was removed or demoted while another domain was
      still unverified.
- [ ] Items the connector cannot do are listed with their dashboard page.
- [ ] For a launch: each checklist item is marked done, missing, or
      "check in the dashboard".

## Guardrails

- **Keep the working address.** Never delete a domain or change
  `primaryDomainId` because another domain is pending. An old domain that
  stays connected keeps redirecting to the primary; removing it stops that.
- **Custom field arrays replace everything.** A `PUT` without an existing
  field deletes it. Changing a field's `type` is rejected (422
  `settings_custom_field_type_immutable`); renaming a `key` orphans the values
  stored under the old key.
- **Board slug and sign-in methods are URL and access changes.** Treat
  `slug`, `signInMethods`, `registrationWallEnabled` and
  `nativeApplicationsEnabled` as high-impact, each with its own confirmation.
- **Passwords stay out of the summary.** Use the password the user gave in
  the call, but do not repeat it in the report.
- **Launch is the user's decision.** Turning protection off makes the board
  public and crawlable at once; list gaps, then let the user choose.

## References

- [references/launch-checklist.md](references/launch-checklist.md): pre-launch,
  launch-day and first-month checks, with the read that proves each one.
- [references/settings-changes.md](references/settings-changes.md): writable
  settings by area, high-impact settings, and what stays in the dashboard.
- [references/domain-and-dns.md](references/domain-and-dns.md): add, DNS,
  verify, make primary, move domains, and fixes for a stuck domain.
- [references/job-form-and-redirects.md](references/job-form-and-redirects.md):
  custom field rules and limits, form layouts, redirect rules and reserved paths.
- [examples/move-to-a-new-domain.md](examples/move-to-a-new-domain.md): a
  domain move from request to verified primary.
