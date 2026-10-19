---
name: tidy-board
description: Reviews and cleans up a Cavuno job board - works through jobs waiting for approval, finds stale, duplicate, spam and incomplete listings, proposes expire or pause instead of delete, and applies confirmed changes in batches of up to 100 with counts and samples. Also checks jobs for Google for Jobs completeness, reviews team members, roles and pending invitations, and turns the Google Indexing API and IndexNow on or off. Use when the user says "tidy up my board", "approve the pending jobs", "find duplicate jobs", "expire jobs older than 60 days", "which jobs are missing a salary?", "remove people who left from the team", "is Google indexing on?" or runs /cavuno:tidy.
---

# Tidy a board

Follow the `cavuno-basics` skill for tool use, confirmations and read-back.

## When to use / not

Use this skill for recurring housekeeping. Not for:

- a read-only health summary: `connect-and-check-board`
- posting or importing new jobs: `post-jobs`, `source-jobs`
- backfill rules and blocked sources: `source-jobs`
- company records, employer accounts and claims: `manage-employers`
- old blog posts: `refresh-and-prune-content`
- merging categories or skills: `organize-taxonomy-and-fields`

## Steps

1. **Baseline.** `read` `/me` (a `member` can manage jobs but not the team
   or integrations), `/usage` (`active_jobs` remaining caps approvals) and
   `/settings` (under `config`: `requireApprovalFreeJobs`,
   `requireApprovalAggregatedJobs`, `defaultJobDurationDays`,
   `passwordProtectionEnabled`).
2. **Collect the groups** the user asked for (all of them for a general
   tidy), using the rules in
   [references/job-review-rules.md](references/job-review-rules.md):
   - **Pending:** `read` `/jobs` `{ status: "pending_approval", limit: 100 }`,
     paging; `read` `/jobs/{id}` for each job you assess (the list omits
     `description`, `applicationUrl` and locations).
   - **Stale:** `query` `/jobs/search` with
     `{ filters: { status: ["published"], publishedAt: { lte: "<ISO date>" } } }`.
     Use the age the user gives, otherwise the board's
     `defaultJobDurationDays` (30 when unset).
   - **Duplicates:** from published and pending jobs, group by company plus
     normalized title, and by application URL; confirm a suspected pair with
     `read` `/jobs` `{ applicationUrl }` or `{ externalId }`.
   - **Incomplete:** published jobs failing the Google for Jobs checks in
     [references/google-for-jobs-completeness.md](references/google-for-jobs-completeness.md).
3. **Present each group:** count, up to five samples (title, company, job
   ID, reason), and one proposed action. If counts came from a capped page,
   say so ("100+").
4. **Map to actions** (job states in the reference):
   - approve → `create` `/jobs/{id}/publish` (counts against `active_jobs`;
     stop before `remaining` runs out)
   - reject a pending job → `delete` `/jobs/{id}` (permanent; own
     confirmation)
   - closed or stale → `/jobs/{id}/expire`; on hold → `/jobs/{id}/pause`
   - duplicate → expire the weaker copy, keep the one with the fuller data
   - incomplete → `PATCH /jobs/{id}` only with values from the employer or
     the source posting; list the rest as "needs the employer"
5. **Apply a confirmed group** with `create` `/jobs/batch`:
   `{ operations: [{ id: "1", method: "POST", action: "expire", resourceId: "<job id>" }, ...] }`,
   at most 100 per call. Check every entry; retry only failures after fixing
   the cause. Read back a sample with `read` `/jobs/{id}`.
6. **Team** (on request or in a full tidy): follow
   [references/team-and-indexing.md](references/team-and-indexing.md).
   List members by role and suspension, and pending or expired invitations,
   as counts plus names only when the user asks about specific people.
7. **Indexing:** `read` `/integrations/indexing`; propose toggles only when
   their prerequisites hold (same reference).
8. **Report** per group: done, failed (with error code), skipped and why,
   and what the user must do in the dashboard or ask the employer for.

Done when:

- [ ] Each group was shown with a count, samples and a proposed action before
      any write.
- [ ] Each group was confirmed separately; every reject or delete had its
      own confirmation.
- [ ] Approvals stopped before `active_jobs` ran out.
- [ ] Every batch entry was checked, and a sample was read back.
- [ ] No job field was filled with a guessed value.

## Guardrails

- **Expire or pause before delete.** Expired jobs leave listings, search and
  the sitemap and their URL returns 404 with no `JobPosting` data; they can
  be published again. Deleting cannot be undone and also removes saved jobs
  and orders.
- **Rejecting is deleting.** A rejected aggregated job is not imported
  again; that is usually the goal, but say it.
- **Do not refresh closed roles.** Extend `expiresAt` or republish only when
  the same role is still open. Never push expiry dates out to make old jobs
  look new.
- **Keep employer titles.** Do not rewrite a supplied job title for search;
  only fix obvious noise (codes, "URGENT", all caps) when the user agrees.
- **Paid jobs are the user's call.** Paid submissions are not auto-expired;
  flag them separately in the stale group.
- **People are not jobs.** Removing or suspending a member, or changing a
  role, each needs its own confirmation by name. The primary owner cannot be
  removed, suspended or demoted.

## References

- [references/job-review-rules.md](references/job-review-rules.md): job
  states, what each action does, and rules for pending, stale, duplicate and
  spam jobs.
- [references/google-for-jobs-completeness.md](references/google-for-jobs-completeness.md):
  fields to check per job and what to do when one is missing.
- [references/team-and-indexing.md](references/team-and-indexing.md): roles,
  invitations, member actions, and the indexing toggles with their errors.
- [examples/weekly-tidy.md](examples/weekly-tidy.md): a full tidy from reads
  to report.
