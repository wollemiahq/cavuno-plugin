# Launch checklist

## Contents

- Before launch
- Tell employers and job seekers
- Launch day
- First week
- Weeks two to four

## Before launch

- [ ] Every export from the old platform is downloaded and stored; the old
      plan is still active.
- [ ] Counts on Cavuno match the inventory (jobs, companies, subscribers,
      redirects), with every difference explained.
- [ ] A test job was posted and applied to; the application or apply
      link works.
- [ ] Payment flow tested in a test environment: checkout, renewal,
      declined card, cancellation, refund (dashboard work; see the
      `set-up-monetization` skill).
- [ ] A test job alert was signed up for and a matching job triggered it.
- [ ] Job pages checked on a phone.
- [ ] A job URL passes Google's Rich Results Test with JobPosting detected.
- [ ] Ten important old paths redirect correctly on the board's current
      Cavuno address; test them again on the custom domain after DNS moves.
- [ ] Analytics records page views on the new site.
- [ ] Someone outside the team used the site once and their stumbles were
      fixed.

## Tell employers and job seekers

Offer to draft these for the user to send; never send them.

- **Employers with live jobs**, one to two weeks before: what is changing,
  the date, that live jobs move over with no action, that paid time
  remaining is honored, and who to contact.
- **Employers on recurring plans**: one clear option, chosen by the user:
  move them to an equivalent plan, let the current plan run out, or give
  credit for the remaining value.
- **Job alert subscribers**: if consent has to be renewed, a "we're moving,
  confirm to keep your alerts" email before the move.
- **Job seekers with accounts**, the day before or on the day: what they
  will need to do on first sign-in. Promise only what was verified.

## Launch day

- [ ] Connect the custom domain (the `launch-and-set-up-board` skill); DNS
      can take up to 24 to 48 hours to spread.
- [ ] Add or verify the Search Console property; if the domain changed,
      add a new property and use Change of Address with both domains
      verified.
- [ ] Submit `https://<domain>/sitemap.xml` in Search Console.
- [ ] Inspect the home page and the most important pages with URL
      Inspection and request indexing.
- [ ] Send the prepared emails.

## First week

- [ ] Search Console Pages report: look for not found pages, redirect
      chains, server errors and robots blocks.
- [ ] Review excluded pages you expected to rank.
- [ ] Watch for a sudden jump in not found errors; check the redirect
      list and add missing rules.
- [ ] Review employer claims and new employer sign-ups (the
      `manage-employers` skill).

## Weeks two to four

- [ ] Compare clicks and impressions with the pre-migration export each
      week.
- [ ] Inspect key pages that seem missing in Search Console rather than
      trusting a `site:` search.
- [ ] Expect rankings to move for 2 to 4 weeks. A large drop that has not
      recovered after 4 weeks points to missed redirects, indexing blocks
      or wrong canonical URLs.
- [ ] Only now let the user cancel the old platform.

Sources: https://cavuno.com/blog/migrate-job-board,
https://cavuno.com/docs/board-setup/301-redirects,
https://cavuno.com/docs/import-export/csv-import-export
