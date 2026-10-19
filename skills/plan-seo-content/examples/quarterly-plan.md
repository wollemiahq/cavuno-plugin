# Example: a quarterly plan for a niche board

## Input

"Plan our blog for next quarter. We run a veterinary jobs board. I have
about five hours a week."

## What Claude found

- `/settings` and `/domains`: "VetCareers", `jobs.vetcareers.example`.
- `/jobs/search` (published): 180 jobs; most in `veterinary-technician`
  and `veterinarian`; about half carry a salary range.
- `/blog/posts` (two pages, `hasMore` false on the second): 23 posts,
  2 drafts.
- Search Console, query by page: "vet tech resume" earns impressions on
  both `/blog/vet-tech-resume-tips` and `/blog/resume-examples-for-vets`.
- No keyword tool is connected, so volume is "not measured".

## Output (abridged)

| Topic | Reader and intent | Evidence | Existing posts | Action | Information gain | Priority |
| --- | --- | --- | --- | --- | --- | --- |
| Vet tech resumes | Seeker, how-to | Search Console: both posts share the query | vet-tech-resume-tips, resume-examples-for-vets | Merge into vet-tech-resume-tips | Examples matched to the skills most requested in the board's vet tech jobs | 1 (blocks the next item) |
| How to hire a vet tech | Employer, how-to | Live results are guides; no board post | none | New article | Salary ranges and common requirements from the board's vet tech jobs | 2 |
| Vet tech salary by state | Seeker, salary data | Search Console impressions for state salary queries at low positions | none | New article | Ranges from the board's salaried jobs, with the job count per state | 3 |
| "Veterinary technician jobs in Ohio" | Seeker, job search | Results are job listings | none | Skip; the Ohio location page serves it | none | none |

Next step offered: "Shall I write the hiring guide with `write-blog-post`,
or plan the merge with `refresh-and-prune-content` first?"

Sources: https://cavuno.com/docs/mcp/cookbook/plan-your-content-strategy,
https://cavuno.com/blog/content-marketing-for-job-boards
