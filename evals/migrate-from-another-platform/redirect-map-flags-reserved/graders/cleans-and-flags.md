---
type: llm
---

PASS if, before creating anything, the reply proposes 301 redirects as
paths (it turns the first row's source into `/category/solar` without the
domain or query string), flags that the `/jobs/1234-solar-installer` source
is under a path Cavuno already uses and cannot be redirected, and either
suggests one wildcard rule for the `/articles/` rows or lists them
individually, then asks the user to confirm.
FAIL if the reply says the redirects were created, keeps the domain or
`?ref=nav` in a source path, or plans the `/jobs/1234-solar-installer`
redirect without any warning.
