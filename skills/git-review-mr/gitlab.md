# GitLab Merge Request operations

## URL and Identity

- `project_path`: everything between host and `/-/merge_requests/` (nested groups kept, e.g. `group/sub/project`)
- `mr_iid`: integer after `merge_requests/` (not the global id)

## MCP

### General

Discover MCP tool schemas with `GetMcpTools` before the first `CallMcpTool` call. Typical tool names: `get_merge_request`, `get_merge_request_diffs`, `list_merge_request_versions`, `mr_discussions`, `get_merge_request_notes`, `get_merge_request_approval_state`, `get_pipeline`, `list_pipeline_jobs`, `create_merge_request_note`, `create_merge_request_thread`.

### Load Sequence

1. `get_merge_request` — title, description, source/target branches, draft, head SHA, pipeline id, web URL.
2. In parallel:
   - `get_merge_request_diffs` with `compact: false` (default `true` truncates hunks). `per_page` 20; increment `page` until a page has fewer than `per_page` items.
   - `mr_discussions` and/or `get_merge_request_notes` (paginate). Treat unresolved threads as already-raised findings.
   - `get_merge_request_approval_state`
   - `list_merge_request_versions` when inline comments may be posted (need `diff_refs`)
3. If a pipeline id is present: `get_pipeline`, then `list_pipeline_jobs` with `scope` `failed` when the pipeline is not success.

For surrounding code not in the hunk, `get_file_contents` with `ref` = source branch. Prefer the local workspace when that remote and branch are already checked out.

## CLI Fallback

When no `*gitlab*` MCP is available and `glab` is installed. Pass `--hostname <host>` when the default instance is not this host. `--repo` is `<host>/<project_path>` or `project_path` depending on glab version.

```
glab mr view <iid> --repo <project_path> --comments
glab mr diff <iid> --repo <project_path>
glab api "projects/<url-encoded-project_path>/merge_requests/<iid>/approvals"
glab api "projects/<url-encoded-project_path>/merge_requests/<iid>/discussions"
glab ci status --repo <project_path>
```

`glab` may accept the MR URI in place of `<iid> --repo ...`; use that when it works.

## Inline Threads

Post only when the user explicitly asks. Prefer MCP. Use CLI only when MCP is absent.

1. Take `base_sha`, `start_sha`, `head_sha` from the latest entry of `list_merge_request_versions` (`diff_refs`). With CLI, GET `projects/.../merge_requests/<iid>/versions` and use the latest `diff_refs`.
2. `create_merge_request_thread` with `position` (CLI: POST `projects/.../merge_requests/<iid>/discussions` with the same fields):

```json
{
  "position_type": "text",
  "base_sha": "<diff_refs.base_sha>",
  "start_sha": "<diff_refs.start_sha>",
  "head_sha": "<diff_refs.head_sha>",
  "old_path": "<path in the old file>",
  "new_path": "<path in the new file>",
  "new_line": 42
}
```

- Added or modified line in the new file: set `new_line` (1-based, new file).
- Deleted line only: set `old_line` instead of `new_line`.
- Unknown line: `create_merge_request_note` (MR-level), or `glab mr note <iid> --repo <project_path> --message "..."`. Do not guess a position.

Keep each thread to one finding. Quote the behavior, not a lecture.
