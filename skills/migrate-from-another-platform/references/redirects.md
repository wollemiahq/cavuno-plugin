# Redirects for a migration

## Contents

- Which URLs to map
- Path rules
- Reserved paths
- Wildcards
- Create in batches
- Test

## Which URLs to map

Start from the Search Console export of top pages (12 months, sorted by
clicks or impressions). Map in this order:

1. Category, location and company pages, and blog posts: they hold links
   and rankings the longest.
2. Live and paid job pages.
3. Expired job pages with traffic or links: send them to the company page
   or the closest category page, or let them return not found; decide with
   the user rather than dropping the whole group silently.

Build the map as two columns, old path and new path. Take the new path
from each record's `links.public` after it is created.

## Path rules

- `fromPath` is a path starting with `/`: no domain, no query string.
- `toPath` is a path or a full URL.
- Point every old URL straight at its final page; avoid chains.
- Use `statusCode: 301` for moved pages (the default). `302` only for a
  temporary move.
- A duplicate `fromPath` returns 409; read the existing rule with `read`
  `/redirects` (`search` matches the start of `fromPath`) and decide
  whether to update it with PATCH `/redirects/{id}`.

## Reserved paths

Redirects run only for paths that match no Cavuno page. You cannot create
redirects for paths Cavuno already uses, including `/jobs`, `/companies`,
`/blog`, `/post`, `/password`, `/auth`, `/home`, `/admin`, `/api`,
`/boards`, `/_next`, `/images`, `/assets`, `/locales`, `/sitemap.xml`,
`/robots.txt`, `/favicon.ico` and `/rss.xml`.

If the old board used the same prefixes (for example old job pages under
`/jobs/...` or company pages under `/companies/...`), those old URLs cannot
be redirected. List them for the user with that reason.

## Wildcards

`(.*)` in the source captures text; `%1`, `%2` place it in the
destination, in the same order:

| Old pattern | `fromPath` | `toPath` |
| --- | --- | --- |
| `/articles/my-post` → `/blog/my-post` | `/articles/(.*)` | `/blog/%1` |
| `/archive/2024/my-post` → `/content/2024/my-post` | `/archive/(.*)/(.*)` | `/content/%1/%2` |

Exact rules are checked before wildcard rules. Use a wildcard only when
the old and new slugs really are the same; otherwise map each URL.

## Create in batches

`create` `/redirects/batch`:

```json
{
  "operations": [
    { "id": "redirect-1", "method": "POST", "body": { "fromPath": "/employers/acme-solar", "toPath": "/companies/acme-solar", "statusCode": 301 } },
    { "id": "redirect-2", "method": "POST", "body": { "fromPath": "/articles/(.*)", "toPath": "/blog/%1", "statusCode": 301 } }
  ]
}
```

Up to 100 operations and 5 MB per call. Check every entry's status; report
409 duplicates and every other refusal with its path and error code.

## Test

Open at least ten important old URLs on the public domain in a private
window and confirm each lands on the right page. For a wildcard, test more
than one value. Also open `/jobs` to confirm no rule intercepts a real
page.

Sources: https://cavuno.com/docs/board-setup/301-redirects,
https://cavuno.com/blog/migrate-job-board, https://cavuno.com/docs/api
