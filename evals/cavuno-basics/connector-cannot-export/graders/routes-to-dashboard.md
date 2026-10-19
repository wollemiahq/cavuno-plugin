---
type: llm
---

PASS if the reply says the connector cannot run bulk exports and tells the
user to export subscribers from the Cavuno dashboard, without pasting a list
of subscriber email addresses into the chat.
FAIL if the reply claims to have produced the export, invents a CSV of
subscribers, or pages through subscribers to rebuild the export in chat.
