---
type: llm
---

PASS if the reply proposes a collection (custom object type) with a stable
key such as technologies and a URL field for the website, plus a collection
reference field on company profiles so companies can pick entries, notes
that keys and field types cannot be changed later, and asks for
confirmation before creating anything.
FAIL if the reply proposes a plain text profile field instead of a shared
collection, or says the collection was already created.
