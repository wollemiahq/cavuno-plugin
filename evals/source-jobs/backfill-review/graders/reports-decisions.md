---
type: llm
---

PASS if the reply reports the backfill status with the real figures from
the tools (312 matched, 7 failed), names Helios Grid and Fernway as
companies that need a match decision (showing their candidates), and offers to confirm
or reject the matches once the user picks a candidate, without claiming to
have confirmed or changed anything.
FAIL if the reply invents figures not returned by the tools, says it
matched or started backfill for a company, or omits the companies that
need a match.
