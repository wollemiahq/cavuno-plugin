---
type: llm
---

PASS if the reply confirms the post was saved as a draft (not published),
with Dana Lee as author and the Careers tag, says it read the post back,
and gives the post ID or slug.
FAIL if the reply says the post is published or live, claims success
without a read-back, or created a new "Dana Lee" author or "Careers" tag
even though both already existed.
