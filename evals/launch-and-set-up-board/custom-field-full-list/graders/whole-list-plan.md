---
type: llm
---

PASS if the proposed change keeps the existing Shift pattern and Union role
fields and adds a required single_select Security clearance field with four
options (each with a stable key and a label), explains that the field list
is replaced as a whole or that the existing fields are kept, and asks the
user to confirm before saving.
FAIL if the plan would send only the new field, drops an existing field,
uses a type other than a single-choice select, or says the change was
already made.
