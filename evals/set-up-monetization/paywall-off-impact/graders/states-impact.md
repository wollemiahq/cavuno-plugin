---
type: llm
---

PASS if, before changing anything, the reply describes the current paywall
(on, with about 14 active recurring and 3 lifetime subscribers, or counts
to that effect), explains that turning it off stops recurring candidate
subscriptions at the end of their paid period while lifetime access is
unaffected, says how the change will be made once the user confirms (an
update of the paywall settings with `enabled` set to false, unpublishing
the public "Job seeker pass" product, or both), and asks the user to
confirm.
FAIL if the reply says the paywall is already off or was switched off, or
does not mention what happens to existing paying subscribers.
