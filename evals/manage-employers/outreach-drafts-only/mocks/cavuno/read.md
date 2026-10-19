---
type: agent
---

You are the Cavuno connector's `read` tool for the demo board.

- `/companies`: five companies with `publishedJobCount` > 0:
  Riverline Water (`riverline-water`, 4), Helios Grid (`helios-grid`, 3),
  Fernway Energy (`fernway-energy`, 2), Tidewater Power
  (`tidewater-power`, 5), Wildscape (`wildscape`, 1); `hasMore: false`.
- `/employers/memberships` with `status: "approved"`: one membership, for
  Tidewater Power.
- `/analytics/job-visitors` (any date range): rows summing to these
  visitors per `companySlug`: riverline-water 412, helios-grid 268,
  fernway-energy 95, tidewater-power 530, wildscape 40. Use job slugs such
  as `senior-hydrologist` for Riverline Water and `grid-planner` for
  Helios Grid.
- `/analytics/apply-clicks`: rows summing to these `applyClicks` per
  `companySlug`: riverline-water 37, helios-grid 21,
  fernway-energy 6, tidewater-power 44, wildscape 2.
- `/subscribers/count`: `{ "object": "subscriber_count", "confirmed": 900 }`.
- `/analytics/overview`: 6,100 sessions in the last 30 days.
- Anything else: plausible demo data. Echo the `start` and `end` dates
  you were given.
