# Gitea pull request operations

## URL and Identity

```
https://<host>/<owner>/<repo>/pulls/<number>
```

- `owner/repo`: the two path segments before `/pulls/`
- `number`: integer after `/pulls/` (the PR index, not a global id)

`/pulls/` (plural) is Gitea. `/pull/` (singular) is GitHub.

## MCP

### General

Discover MCP tool schemas with `GetMcpTools` before the first `CallMcpTool` call. Do not assume tool names; look up tools that cover PR metadata, files/diff, comments, reviews, and commit statuses.

### Load Sequence

After discovering schemas, load in parallel where the tools allow:

1. PR metadata — title, body, base/head branches, draft, head SHA, url, mergeable.
2. Files and diff — full hunks; paginate until a short page.
3. Issue comments and review comments. Treat unresolved review threads as already-raised findings.
4. Reviews / approval state.
5. Commit statuses or Actions for the head SHA.

For surrounding code not in the hunk, fetch the file at the head ref. Prefer the local workspace when that remote and branch are already checked out.

## CLI Fallback

When no `*gitea*` MCP is available and the Gitea CLI `tea` is installed. Pass `--repo <owner>/<repo>`. Pass `--login <name>` for the login that matches this host (`tea logins`). Do not infer the repo from `$PWD`.

```
tea pr <number> --repo <owner>/<repo> --login <login> --comments --output json \
  --fields index,title,body,state,url,base,head,base-commit,diff,patch,mergeable,comments
```

`diff` / `patch` on that command are the PR diff. For reviews, files, and statuses (no first-class `tea` subcommand), use the Gitea API:

```
GET /api/v1/repos/<owner>/<repo>/pulls/<number>/files
GET /api/v1/repos/<owner>/<repo>/pulls/<number>/reviews
GET /api/v1/repos/<owner>/<repo>/pulls/<number>/reviews/<id>/comments
GET /api/v1/repos/<owner>/<repo>/issues/<number>/comments
GET /api/v1/repos/<owner>/<repo>/statuses/<head_sha>
```

If `tea` exposes an `api` subcommand, use it for those paths. Otherwise stop and ask before `curl`.

## Inline Comments

Post only when the user explicitly asks. Prefer MCP. Use CLI/API only when MCP is absent. Do not call `tea pr approve` / `tea pr reject` as part of posting.

- Known line: POST `/api/v1/repos/<owner>/<repo>/pulls/<number>/reviews` with `event` `COMMENT` (not approve/reject):

```json
{
  "event": "COMMENT",
  "commit_id": "<head_sha>",
  "comments": [
    {
      "path": "<file>",
      "body": "...",
      "new_position": 42
    }
  ]
}
```

- Added or modified line in the new file: `new_position` (1-based).
- Deleted line only: `old_position` instead of `new_position`.
- Unknown line: `tea comment <number> --repo <owner>/<repo> --login <login>` with the body as the argument if `tea` accepts it; otherwise POST `/api/v1/repos/<owner>/<repo>/issues/<number>/comments` with `{"body":"..."}`. Do not guess a position.

Keep each comment to one finding. Quote the behavior, not a lecture.
