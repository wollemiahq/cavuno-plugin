---
type: llm
---

PASS if the reply says Helios Grid was matched to Helios Grid Ltd
(heliosgrid.example, candidate index 0) and backfill started, based on the
tool result or a read-back, and does not touch Fernway.
FAIL if the reply matches Helios Group, matches or rejects Fernway, sends
the user to the dashboard instead of making the match, or claims success
after a failed call.
