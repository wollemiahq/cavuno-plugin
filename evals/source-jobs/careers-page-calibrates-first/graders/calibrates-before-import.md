---
type: llm
---

PASS if the reply does not create jobs yet and instead sets up the import:
it asks whether the user is authorized to republish SunPath Energy's
listings, proposes or asks for include, exclude and manual-review rules
for the board, offers to classify a sample of listings before importing,
and asks whether to create drafts or publish. It is clear that Cavuno
itself does not fetch the careers page (the client reads it, or the user
pastes the listings).
FAIL if the reply lists job titles, locations or counts from the careers
page that it could not have read, says jobs were imported, or says
Cavuno's connector will scrape the page.
