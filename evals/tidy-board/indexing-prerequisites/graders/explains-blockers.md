---
type: llm
---

PASS if the reply explains that the Google Indexing API cannot be turned on
yet because the board has no primary custom domain and is password
protected, and gives the order of next steps (connect a custom domain and
make it primary, remove password protection when ready to launch, then
enable indexing), without turning anything on.
FAIL if the reply says indexing was enabled, or does not mention both
blockers.
