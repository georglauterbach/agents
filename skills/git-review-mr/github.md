# GitHub pull request operations

## URL and Identity

- `host`: `github.com` or a GitHub Enterprise host
- `owner/repo`: the two path segments before `/pull/`
- `number`: integer after `/pull/` (not the global graph id)

## MCP

### General

Discover MCP tool schemas with `GetMcpTools` before the first `CallMcpTool` call. Do not assume tool names; look up tools that cover PR metadata, files/diff, review comments, reviews, and checks.

### Load Sequence

After discovering schemas, load in parallel where the tools allow:

1. PR metadata — title, body, base/head branches, draft, head SHA, url, review decision.
2. Files and diff — full hunks; paginate until a short page.
3. Review comments and issue comments. Treat unresolved review threads as already-raised findings.
4. Reviews / approval state.
5. Checks or status for the head SHA.

For surrounding code not in the hunk, fetch the file at the head ref. Prefer the local workspace when that remote and branch are already checked out.

## CLI Fallback

When no `*github*` MCP is available and `gh` is installed. `gh` accepts the PR URI; otherwise pass `--repo <owner>/<repo>`. For GitHub Enterprise, `GH_HOST=<host>` or `gh --hostname <host>`.

```
gh pr view <uri> --json title,body,baseRefName,headRefName,headRefOid,isDraft,url,reviews,comments,reviewDecision,statusCheckRollup
gh pr diff <uri>
gh pr checks <uri>
gh api repos/<owner>/<repo>/pulls/<number>/comments
gh api repos/<owner>/<repo>/pulls/<number>/reviews
```

## Inline Comments

Post only when the user explicitly asks. Prefer MCP review-comment tools. Use CLI only when MCP is absent.

- Known line: create a pending or single review comment on `path` + `line` (1-based, new file) at `commit_id` = head SHA. Deleted-line-only: use the old side / `original_line` equivalent. Do not guess a position.
- Unknown line: `gh pr comment <uri> --body "..."` (request-level).

CLI inline example:

```
gh api repos/<owner>/<repo>/pulls/<number>/comments \
  -f body="..." -f path="<file>" -F line=42 -f side=RIGHT -f commit_id="<head_sha>"
```

Keep each comment to one finding. Quote the behavior, not a lecture.
