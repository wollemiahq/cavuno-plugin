# Cavuno tools in detail

## Contents

- Find an operation with `search`
- Call an operation on the connector
- Call an operation on the code-mode endpoint
- Search the documentation
- Upload an image
- Report a problem

## Find an operation with `search`

`search` takes `code`: the source of an async arrow function. Inside it,
`index` is a slim catalog, `spec` is the OpenAPI document with `$ref`s
unresolved, and `deref(node)` expands a subtree. There is no network and no
account data.

```javascript
// Every path that mentions jobs, with its methods
async () => Object.entries(index.paths)
  .filter(([path]) => path.startsWith('/jobs'))
  .map(([path, ops]) => ({ path, methods: Object.keys(ops) }))

// The query parameters a list accepts
async () => index.paths['/jobs'].get.parameters.filter((p) => p.in === 'query')

// The full request body for a create
async () => deref(spec.paths['/jobs'].post.requestBody)
```

Start from `index`, then `deref` only the part you need. A list's
`detail.extraFields` (such as a description) are returned only by the detail
operation, not by the list.

## Call an operation on the connector

| Tool | Arguments |
| --- | --- |
| `read` | `{ path, query? }`, GET only |
| `query` | `{ path, body, query? }` for the five search POSTs |
| `create` | `{ path, body?, query?, idempotencyKey? }`, POST |
| `update` | `{ method: 'PATCH' or 'PUT', path, body, query?, idempotencyKey? }` |
| `delete` | `{ path, query?, idempotencyKey? }` |

`idempotencyKey` is sent as the `Idempotency-Key` header; see the
writes-and-errors reference of this skill for when to send and reuse one.

`path` is relative to the API base, for example `/jobs/{id}` with the real
ID filled in. Lists are paginated: follow the cursor or page fields the
operation documents until you have everything you need, and say so when you
stop early.

Every call returns `{ status, ok, data }`. A list's records are usually in
`data.data`.

## Call an operation on the code-mode endpoint

Clients on `https://mcp.cavuno.com/mcp` get `execute` instead. It runs one
async arrow function with a `cavuno.request` binding and returns whatever the
function returns.

```javascript
async () => {
  const r = await cavuno.request({ method: 'GET', path: '/jobs', query: { status: 'published', limit: 25 } });
  if (!r.ok) return { failed: r.status, error: r.data.error };
  return r.data.data.map((job) => ({ id: job.id, title: job.title }));
}
```

Never add an `Authorization` header; the server attaches the credential.
`headers` may carry `Idempotency-Key`. One `execute` call should finish in a
few seconds, so split long work across calls.

## Search the documentation

`search_docs` takes `{ query }`, a plain-language question of up to 500
characters, and returns up to six sections with `title`, `url` and
`content`. Cite the `url` of anything you rely on. If no result answers the
question, say the docs do not cover it instead of guessing.

## Upload an image

Use this for a blog cover, a company logo or a board logo or hero.

1. Call `create_media_upload_intent` with `filename`, `mimeType`, the exact
   `sizeBytes` (10 MB at most) and `purpose`: one of `board_logo`,
   `board_hero`, `account_avatar`, `company_logo`, `blog_image`.
2. POST the raw file bytes to the returned `uploadUrl` with the file's real
   `Content-Type`. The response holds a `storageId`. This step needs a
   client that can send HTTP requests with a file, such as Claude Code. In a
   chat with no such ability, ask the user to upload the image in the
   Cavuno dashboard instead.
3. Call `complete_media_upload_intent` with the intent ID and `storageId`.
   The result is a media object; assign its `id` to fields such as
   `coverMediaId` or `ogImageMediaId`.

The `uploadUrl` is a short-lived credential. Do not show it to the user or
paste it anywhere else.

## Report a problem

`submit_feedback` takes `type` (`missing_capability`, `bug`,
`unclear_documentation`, `unexpected_response`, `unhelpful_error`,
`performance` or `other`), `goal`, `message`, and optionally `endpoint`,
`method`, `outcome` (`blocked`, `degraded`, `completed`), `expected`,
`suggestion`, `request_id` and `agent` (`name`, `model`). Leave out
credentials, personal data and the user's private content.

Sources: https://cavuno.com/docs/mcp,
https://cavuno.com/docs/mcp/reference/docs-search,
https://cavuno.com/docs/mcp/reference/media
