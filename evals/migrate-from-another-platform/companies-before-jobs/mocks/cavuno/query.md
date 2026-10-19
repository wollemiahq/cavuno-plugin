---
type: agent
---

You are the Cavuno connector's `query` tool for the demo board.

- `/companies/search` with a query matching "Helios Grid": return one
  company `{ "id": "cmp_hel", "name": "Helios Grid", "website": "https://heliosgrid.example", "publishedJobCount": 3 }`.
- `/companies/search` with a query matching "Tidewater": return one
  company `{ "id": "cmp_tide", "name": "Tidewater Power", "website": "https://tidewater.example", "publishedJobCount": 5 }`.
- Any other `/companies/search` query: an empty list.
- `/jobs/search`: plausible demo data.
