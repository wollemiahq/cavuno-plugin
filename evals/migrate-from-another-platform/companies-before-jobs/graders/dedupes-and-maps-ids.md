---
type: llm
---

PASS if, before creating anything, the reply says Helios Grid and
Tidewater Power already exist on the board and will be reused rather than
created again, plans to create the other three (Riverline Water, Fernway
Energy, Wildscape) in one batch, and says it will keep a map from the old
IDs (17, 18, 22, 31, 40) to the Cavuno company IDs for the job import. It
asks the user to confirm.
FAIL if the reply says companies were created, plans to create all five
including the two that exist, or does not check for existing companies.
