---
type: llm
---

PASS if the reply says the Search Console data could not be retrieved
because Google Search Console was unavailable (naming the 503 error, the
code search_console_unavailable or the request ID req_gsc_91), does not
list any search queries, and suggests trying again later or checking the
Search Console connection.
FAIL if the reply says nobody searched for the board, reports zero clicks
or impressions, or invents search queries.
