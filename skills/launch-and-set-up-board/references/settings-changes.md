# Settings changes

`PATCH /settings` sets only the keys sent; everything else stays. `GET
/settings` returns most values under `config`. Confirm the exact key with
`search` before writing; this table is a map, not the schema.

## Writable settings by area

| Area | Keys |
| --- | --- |
| Identity | `name`, `slug`, `contactEmail`, `companyLegalName`, `companyAddress`, `companyWebsiteUrl`, social URLs (`companyXHandle`, `companyFacebookUrl`, `companyLinkedinUrl`, `companyInstagramUrl`) |
| Domain | `primaryDomainId` (an active domain's `id` from `/domains`) |
| Images | `logoMediaId`, `heroMediaId` (a media ID from an upload with purpose `board_logo` or `board_hero`) |
| Features | `jobAlertsEnabled`, `blogEnabled`, `candidatesEnabled`, `employersEnabled`, `impressumEnabled`, `jobRecommendationsEnabled`, `recommendedTalentEnabled`, `applicantMessagingEnabled` |
| Posting and approval | `requireApprovalFreeJobs`, `requireApprovalAggregatedJobs`, `postingRequiresMembership`, `defaultJobDurationDays` |
| Applications | `nativeApplicationsEnabled`, `applyUrlAttribution`, `countryGatingMode` (`sponsored_only` or `all_jobs`) |
| Access | `registrationWallEnabled`, `signInMethods` (per role: `password`, `magicLink`, `google`, `linkedin`) |
| Talent | `talentDirectoryVisibility` (`off`, `public`, `employers_only`), `defaultCandidateProfileVisibility` (`hidden`, `logged_in_only`, `public`), `talentNavLabel` |
| Privacy | `requireCookieConsent`, `cookieBanner*` labels |
| Branding | `showCavunoBranding` (plan-gated) |

Contact and company fields accept `null` to clear.

## High-impact settings (own confirmation each)

| Key | What happens |
| --- | --- |
| `slug` | The board's URL slug changes. Reserved values are rejected. |
| `primaryDomainId` | Canonical links, emails and structured data move to that domain; other active domains redirect to it. Only set it to an active domain. |
| `nativeApplicationsEnabled: false` | Every published job without an external `applicationUrl` is unpublished back to draft, in the background. |
| `registrationWallEnabled: true` | Visitors must sign in to view jobs. |
| `signInMethods` | Switching off a role's last sign-in method is refused (409 `sign_in_method_last`). People already signed in stay signed in. |
| `postingRequiresMembership: true` | Only companies with an active plan assignment can post. |
| `requireApprovalFreeJobs`, `requireApprovalAggregatedJobs` | Affect new jobs only; live jobs stay live. Paid posts, imports, feeds and operator-created jobs never wait. |
| `talentDirectoryVisibility` | Controls who can browse candidate profiles. |
| `defaultCandidateProfileVisibility` | Applies at signup only; existing profiles are not changed. |
| `countryGatingMode: all_jobs` | Also country-checks ordinary apply actions on compatible starter boards. |

## Password protection

| Goal | Call | Notes |
| --- | --- | --- |
| Turn on or change the password | `POST /settings/password-protection` `{ password }` | At least 8 characters; one shared password for all visitors |
| Turn off | `DELETE /settings/password-protection` | 403 on the Free plan |

While protected: search engines cannot crawl the board, the jobs widget is
off, and Google Indexing API and IndexNow notifications are skipped.
`passwordProtectionMessage` is deprecated and no longer shown; the
coming-soon page is designed at `/coming-soon-gate` in the AI website
builder on paid plans.

## Settings the connector cannot change

Theme, pages, navigation, footer and SEO page titles (AI website builder);
the custom email domain and email templates; Stripe, products and tax;
Google Search Console and analytics connections; SSO connection testing;
built-in job form options such as allowed employment types, currencies and
salary bounds (Settings → Job form). Read `/settings/job-form` to report
their current values.

## Before/after table format

| Setting | Now | After | Visitors will notice |
| --- | --- | --- | --- |
| Job alerts | off | on | Visitors can subscribe to email alerts for new jobs |

Sources: https://cavuno.com/docs/mcp/reference/settings,
https://cavuno.com/docs/board-setup/password-protection,
https://cavuno.com/docs/managing-jobs/job-moderation,
https://cavuno.com/docs/board-setup/connect-custom-domain,
https://cavuno.com/docs/board-setup/job-form-settings
