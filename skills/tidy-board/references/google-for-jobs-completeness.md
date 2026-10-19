# Google for Jobs completeness

Cavuno adds `JobPosting` structured data to every published job from the
job's fields. Missing or wrong fields can keep a job out of Google's job
search experience. Valid data makes a job eligible; it never guarantees
display, ranking or timing.

The `/jobs` list returns title, status, company ID, employment type, remote
option, seniority, salary, dates, skills and categories. `description`,
`applicationUrl`, `officeLocations` and remote eligibility come only from
`GET /jobs/{id}`; read details for a sample, or for every job when the user
asks for a full audit.

## Checks per published job

| Check | Field | Required by Google | Fail when | Fix |
| --- | --- | --- | --- | --- |
| Clean title | `title` | Yes | Contains a company name, location, salary, job code (such as "REQ-4521"), "URGENT", emoji or all caps | Suggest a cleaned title; apply only with the user's yes, and never rewrite a supplied title just for search |
| Full description | `description` | Yes | Empty, a teaser, or missing responsibilities and requirements | Ask the employer or use the source posting; there is no minimum word count |
| Hiring company | `companyId` | Yes | `null` | Attach the company (`manage-employers` for new records) |
| Date posted | `publishedAt` | Yes | `null` on a published job | Report it; it is set at publish |
| Location for on-site and hybrid | `officeLocations` | Yes, for a physical workplace | Empty on `remoteOption` `on_site` or `hybrid` | Add city, region and country from the source; Cavuno never invents street addresses |
| Remote eligibility | `remotePermits` | Yes, for fully remote jobs | Empty on `remoteOption: remote` | Add the countries or regions from the source, using values from `/taxonomies/remote-permits` |
| Expiry | `expiresAt` | Required when the posting has an end date | In the past on a published job, or set far out to keep a closed role live | Expire the job, or set the real deadline |
| Employment type | `employmentType` | Recommended | Missing | Set from the source |
| Salary | `salaryMin`, `salaryMax`, `salaryCurrency`, `salaryTimeframe` | Recommended | Missing when the description states pay | Copy the stated range; never estimate |
| Apply path | `applicationUrl` | Not a Google field | Empty or broken | Ask the employer |
| Education and experience | `educationRequirements`, `experienceMonths` | Recommended (beta) | Stated in the description but not set | Set from the description |

Search Console warnings about a missing `streetAddress` or `postalCode` are
non-critical ("Improve item appearance"); the jobs stay eligible. Do not
add placeholder addresses.

Use `TELECOMMUTE` (remote) only for fully remote roles. A hybrid role keeps
its physical location.

## How to report

Group failures by check, with counts and five samples each. Split each group
into "I can fix from the posting text" and "needs the employer". Propose
`PATCH /jobs/{id}` batches only for the first kind.

## Verify

After edits, the user can test a job URL in Google's Rich Results Test or
Cavuno's free schema validator, and inspect it in Search Console. With the
Google Indexing API on, edits notify Google; Google still decides when to
recrawl.

Sources: https://cavuno.com/docs/job-board-seo/google-for-jobs,
https://cavuno.com/docs/troubleshooting,
https://cavuno.com/blog/job-posting-schema,
https://cavuno.com/blog/google-indexing-api-job-postings
