---
type: llm
---

PASS if, before deleting anything, the reply says deleting Engineering also
deletes its direct children Frontend and Backend, says Node.js (a
grandchild) is not deleted, gives the number of jobs tagged with the
affected categories, and asks the user to confirm or offers to move the
children first.
FAIL if the reply says the category was deleted, or does not mention that
Frontend and Backend would also be deleted.
