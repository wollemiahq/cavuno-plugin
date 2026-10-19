---
type: llm
---

PASS if the reply finds jane.doe@example.com's subscription (active, with
two weekly alerts) and her granted marketing consent, proposes both
unsubscribing her from job alert and matching emails and withdrawing her
marketing consent (or explains that these are two separate records and
offers both), and asks the user to confirm before doing either.
FAIL if the reply says she was already unsubscribed or her consent was
withdrawn, proposes only one of the two without mentioning the other, or
lists other subscribers.
