---
name: migrate-from-another-platform
description: Moves an existing job board from another platform onto Cavuno. Inventories what the old board holds, routes each data type to the right path (CSV and XML file imports are dashboard-only; companies and jobs can also go through the API in batches of 100), creates companies before their jobs, keeps old job IDs as external IDs, builds an old-to-new URL map and creates 301 redirects in batches, checks employer accounts, and runs a launch-day checklist with Search Console monitoring. Use when the user says "migrate my board from Jobboardly", "move our jobs and companies to Cavuno", "import my old board", "set up redirects from our old URLs", "what do I need to do on launch day?" or "we're switching platforms".
---

# Migrate from another platform

Follow the `cavuno-basics` skill for tool use, confirmations and read-back.

## When to use

- Moving companies, jobs, employers, subscribers and blog URLs from
  another job board platform, spreadsheet or ATS export.
- Building redirects from old URLs and checking the switch-over.

Not here: ongoing feeds or careers-page syncs after launch
(`source-jobs`); connecting the custom domain and board setup
(`launch-and-set-up-board`); recreating pricing plans
(`set-up-monetization`); writing or rewriting blog posts
(`write-blog-post`); one-off job posts (`post-jobs`).

## Steps

1. **Inventory the old board.** Ask for exports of jobs, companies,
   employer accounts, subscribers, blog posts and the top pages from
   Search Console for the last 12 months. Record a count for each; these
   counts are the migration's acceptance test. Tell the user to finish
   every export before cancelling the old plan.
2. **Route each data type** with the table in
   [references/migration-plan.md](references/migration-plan.md). Large
   files go through the dashboard importer (Jobs → Import, Companies →
   Import, Job Alerts → Import); the connector cannot upload files.
   Through the API: companies, jobs and redirects in batches.
3. **Read the target board.** `read` `/usage` (`active_jobs.remaining`
   against the number of live jobs to bring), `/settings/job-form`,
   `/taxonomies/categories`, and `/companies` to see what already exists.
4. **Clean the data with the user**: drop old expired jobs, duplicates and
   inactive companies; map fields and custom fields (reference).
5. **Rehearse** with five companies and five of their jobs: create, read
   back, open `links.public`, check the job pages. Fix the mapping before
   the full run.
6. **Companies first.** `query` `/companies/search` for each name and
   compare website domains; reuse matches. Then `create` `/companies/batch`
   with up to 100 `{ id, method: "POST", body: { name, website, ... } }`
   operations for the rest. A 409 entry means a company with that domain
   or name exists: look it up and reuse it. Keep a map from old company ID
   to Cavuno ID.
7. **Then jobs.** `create` `/jobs/batch` in chunks of 100 with `companyId`
   from the map, `externalId` set to the old platform's job ID (for
   example `oldboard:1234`), the remaining `expiresAt` (Unix
   milliseconds) and, for jobs created published, the original
   `publishedAt`. Live jobs may be created with
   `status: "published"` once the user confirms; everything else as
   drafts. Check every entry and retry only failures.
8. **Build the URL map and redirects** with
   [references/redirects.md](references/redirects.md): old path to new
   public path, then `create` `/redirects/batch` with up to 100
   `{ id, method: "POST", body: { fromPath, toPath, statusCode: 301 } }`
   operations. Read the list back with `read` `/redirects`.
9. **Check employers.** Employer accounts cannot be created through the
   API. Make sure each company has the right `website` so employers who
   sign up with a work email on that domain join automatically; invite the
   rest from the dashboard (Companies → Manage team). After launch, review
   `/employers/claims` with the `manage-employers` skill.
10. **Launch and monitor** with
    [references/launch-checklist.md](references/launch-checklist.md).
11. **Report** counts against the inventory for every data type, the
    redirects created and refused, and the dashboard tasks left.

Done when:

- [ ] created counts match the inventory for companies and jobs, with every difference explained;
- [ ] every job has the old platform's ID as `externalId` and links to the right company;
- [ ] every high-traffic old URL has a 301 to a live page, or is listed as not redirectable with the reason;
- [ ] dashboard-only work (file imports, subscribers, domain, payments) is listed for the user;
- [ ] the launch checklist was handed over with dates.

## Guardrails

- Do not cancel or change anything on the old platform; that is the
  user's step after verification.
- Subscribers imported in the dashboard are confirmed immediately and get
  no confirmation email. Import only people who consented; ask whether the
  user needs a re-confirmation email first.
- Paid listings and running employer subscriptions do not carry over by
  themselves. Keep paid jobs' remaining time through `expiresAt`, and plan
  billing with the `set-up-monetization` skill.
- Keep each job's original application URL; do not switch external-apply
  jobs to native applications during the migration.
- Recreate jobs only from the export; never fill missing descriptions or
  salaries.

## References

- [references/migration-plan.md](references/migration-plan.md): data type routing, order of work, field mapping, cleaning rules, verification counts.
- [references/redirects.md](references/redirects.md): URL map rules, wildcards, reserved paths, batch format, testing.
- [references/launch-checklist.md](references/launch-checklist.md): before launch, launch day, and the following four weeks.
