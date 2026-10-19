---
name: post-jobs
description: Posts jobs to a Cavuno job board from pasted text, a job URL or a list of up to 100 roles. Finds or creates the hiring company, maps the role to the board's categories, skills, remote permits and timezones, checks for duplicates by external ID and application URL, creates drafts by default, handles 409 duplicate responses, reads every job back and approves jobs awaiting approval. Use when the user says "post this job", "add this role to my board", "here's a job link, put it on the board", "upload these 20 jobs", "publish the draft I just made" or "approve the job that's waiting".
---

# Post jobs

Follow the `cavuno-basics` skill for tool use, confirmations and read-back.

## When to use

- One job from pasted text or a job page URL.
- A list of up to 100 roles (pasted table, spreadsheet rows, a partner's list).
- Publishing a draft, or approving a named job that is `pending_approval`.

Not here: syncing an employer's careers page or reviewing backfill
(`source-jobs`); moving a whole board (`migrate-from-another-platform`);
working through the whole approval queue or stale jobs (`tidy-board`);
creating new categories, skills or custom fields
(`organize-taxonomy-and-fields`).

## Steps

1. **Get the complete source.** Use pasted text as given. For a URL, read
   the page with the client's own web fetch or browser; Cavuno cannot
   fetch pages. Follow the official application or ATS link when the page
   is only a summary. If the full description cannot be read, ask the user
   for it; never fill gaps yourself. If the listing belongs to an employer
   other than the user, confirm they are authorized to republish it.
2. **Extract the fields** with [references/job-fields.md](references/job-fields.md):
   title, actual hiring company and its homepage, full description,
   `applicationUrl`, `externalId`, `remoteOption` with locations or remote
   permits, employment type, seniority, salary.
3. **Load the board's vocabulary once per conversation.** `read`
   `/taxonomies/categories` (pass `search`) and `/taxonomies/skills`
   (`search`), `/settings/job-form` for custom employment types and custom
   fields, and for remote roles `/taxonomies/remote-permits` and
   `/taxonomies/remote-timezones`. Map only to slugs that exist. When no
   category or skill clearly fits, leave the field out: Cavuno tags new jobs
   automatically.
4. **Resolve the company without writing.** `query` `/companies/search`
   with `{ query: "<name>" }` and compare website domains. Record either the
   existing company `id` or "new company: name + homepage".
5. **Check for duplicates.** `read` `/jobs` with `{ externalId }`, then with
   `{ applicationUrl }`. A match with the same title is the same job: offer
   to update it with `update` PATCH `/jobs/{id}` instead of creating. Details
   in [references/duplicates-and-approval.md](references/duplicates-and-approval.md).
6. **Check capacity** before more than five jobs or any publish: `read`
   `/usage` and note `remaining` on the `active_jobs` capacity.
7. **Show the plan.** One job: a table of every field you will send, the
   company outcome and the status (`draft` unless the user asked to
   publish). A list: the count, the first five rows, and every row you will
   skip or that needs an answer. Ask only about material ambiguities
   (location, remote eligibility, employment type, salary currency).
8. **Write after a clear yes**, each request with a fresh `idempotencyKey`.
   - Company: `create` `/companies/find-or-create` with `{ name, website }`.
     `matched: true` means it already existed.
   - One job: `create` `/jobs` with `companyId`. Omit `status` for a draft;
     send `status: "published"` only when the user asked to publish.
   - A list: `create` `/jobs/batch` with up to 100
     `{ id, method: "POST", body }` operations, then check every entry.
   - On a 409 `jobs_already_exists`, follow
     [references/duplicates-and-approval.md](references/duplicates-and-approval.md).
9. **Publish or approve on request.** `create` `/jobs/{id}/publish` (body
   optional `{ expiresAt }` as ISO 8601). It publishes drafts and expired
   jobs and approves `pending_approval` jobs. Never PATCH `status`.
10. **Read back.** `read` `/jobs/{id}` for each job and compare status,
    company, title, `externalId`, `applicationUrl`, location and the
    description length with the plan.
11. **Report** each job's ID, title, company, status, `links.admin`, and
    `links.public` for published jobs; list skipped rows and failures with
    their error codes.

Done when:

- [ ] every created or changed job was read back and matches the plan;
- [ ] status is `draft` unless the user asked to publish or approve;
- [ ] every job has an `externalId` (source ID or source URL);
- [ ] no duplicate company was created (`matched` checked or search done);
- [ ] every batch entry is reported as created, skipped or failed with its code.

## Guardrails

- Keep the description complete: responsibilities, requirements, salary,
  benefits, how to apply. Do not summarize, rewrite or add a "Source:"
  footer; the original destination belongs only in `applicationUrl`.
- Use the actual hiring company, not the recruiter, job board or ATS host,
  unless the user says the agency itself is the employer.
- Send `allowSharedApplicationUrl: true` only after a 409 with
  `matched_on: "application_url"` whose matching jobs are different roles.
- `salaryMin` or `salaryMax` needs both `salaryCurrency` and
  `salaryTimeframe`. If the source omits the currency, ask; do not guess
  from the location.
- Job create takes `expiresAt` as Unix milliseconds; `/jobs/{id}/publish`
  takes ISO 8601. Omit it to use the board default (30 days when unset).
- A job cannot be created as `pending_approval`; that status only comes
  from moderation of free posts and backfill.

## References

- [references/job-fields.md](references/job-fields.md): source-to-field mapping, enums, remote and location rules, custom fields.
- [references/duplicates-and-approval.md](references/duplicates-and-approval.md): pre-write duplicate checks, 409 decisions, publish and approval behavior.
- [examples/job-from-url.md](examples/job-from-url.md): one job from a URL, from plan to report.
