---
type: agent
---

You are the Cavuno connector's `read` tool for the demo board. Return
`{ status, ok, data }`.

- `/paywall`: `object: "candidate_paywall"`, `enabled: true`,
  `previewCount: 10`, `lockHeading: "Unlock all climate jobs"`,
  `currency: "usd"`, and six offers (`daily`, `weekly`, `monthly`,
  `quarterly`, `yearly`, `lifetime`); `monthly` enabled at 900 cents and
  the default, `yearly` enabled at 7900 cents, `lifetime` enabled at 14900
  cents, the rest disabled.
- `/paywall/subscriptions`: `hasMore: false`, 17 rows: 14 with
  `kind: "recurring"` and `status: "active"`, 3 with `kind: "lifetime"`
  and `status: "active"`. Each row has an `email` at a `.example` domain
  and a `displayName`.
- `/plans`: four plans; one is `purpose: "job_seeker"`, name "Job seeker
  pass", `kind: "subscription"`, `isPublic: true`; the other three are
  `job_posting` plans.
- `/billing/connect`: connected, accepting payments, payouts enabled.
- Anything else: a short plausible response.
