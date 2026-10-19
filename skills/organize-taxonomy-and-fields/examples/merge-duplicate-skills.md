# Example: merge duplicate skills

## Input

"We have both 'Dev Ops' and 'DevOps' as skills. Merge them."

## Plan

1. `read` `/taxonomies/skills` with `{ search: "devops" }` and
   `{ search: "dev ops" }`, paging until `hasMore` is false:
   - `skl_1` "DevOps", slug `devops`, aliases none
   - `skl_2` "Dev Ops", slug `dev-ops`, aliases none
2. `query` `/jobs/search` `{ filters: { skills: ["dev-ops"] }, limit: 100 }`:
   7 jobs. Same for `devops`: 31 jobs. Keep `devops`.
3. Show:

| Step | Change | Affects |
| --- | --- | --- |
| 1 | Replace `dev-ops` with `devops` on 7 jobs (2 already have both; they end with `devops` once) | 7 jobs, e.g. "Platform Engineer" (job_a1), "SRE" (job_a7) |
| 2 | Delete skill "Dev Ops" | 0 jobs after step 1 |
| 3 | Add `dev-ops` as an alias of "DevOps" | Old `dev-ops` links resolve to DevOps |

   "Shall I run step 1? Steps 2 and 3 need their own yes after I check
   step 1 worked."

## After each yes

- Step 1: `create` `/jobs/batch` with 7 `PATCH` sub-operations, each body
  `{ skills: [<the job's full list with dev-ops swapped>] }`. All 7 entries
  succeeded. Re-run the `dev-ops` count: 0.
- Step 2: `delete` `/taxonomies/skills/skl_2` → 204.
- Step 3: `create` `/taxonomies/skills/skl_1/aliases`
  `{ aliases: ["dev-ops"] }` → `read` `/taxonomies/skills/skl_1`:
  `aliasSlugs` includes `dev-ops`.

## Output

Merged "Dev Ops" into "DevOps": 7 jobs re-tagged (36 jobs now tagged
DevOps), "Dev Ops" deleted, and `dev-ops` kept as an alias so old links
still work.
