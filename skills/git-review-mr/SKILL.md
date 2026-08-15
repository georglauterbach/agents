---
name: git-review-mr
description: Reviews a merge request or pull request from a URI.
disable-model-invocation: true
license: MIT
---

# Review Merge Request

Review a merge request from an URI. Report findings in chat. Do not post, merge, implement, restart, approve, deny, comment or push anything unless the user explicitly asks.

## Input

Require a merge request URI. If none is given, ask. Do not infer the request.

Parse platform and identity from the URI, choose a platform and read the adapter file:

|Platform|URI shape|Identity|Adapter File|MCP Name Matches|
|:-|:-|:-|:-|:-|
|GitLab|`https://<host>/<project_path>/-/merge_requests/<iid>`|`project_path`, `iid`|[`gitlab.md`](gitlab.md)|`*gitlab*`|
|GitHub|`https://<host>/<owner>/<repo>/pull/<number>`|`owner/repo`, `number`|[`github.md`](github.md)|`*github*`|
|Gitea|`https://<host>/<owner>/<repo>/pulls/<number>`|`owner/repo`, `number`|[`gitea.md`](gitea.md)|`*gitea*`|

When you encounter an unknown platform, tell the user and exit.

An optional description after the URI provides context on the review's intent.

## Fetch

Use the tools laid out in the platform adapter:

1. MCP Servers: If a ready server matches the platform, use it for all forge API operations. Do not use a CLI, `curl`, or a browser while that MCP is available.
2. Else, Platform CLI: If installed, use it.
3. Else: Stop and ask whether to proceed with basic tools (WebFetch, `curl`). Do not scrape unprompted.

Load, in parallel where possible:

- Metadata (title, description, branches, draft, head SHA)
- File diffs (full hunks; paginate until a short page)
- Discussions and notes
- Approval / review state
- Pipeline or check status for the head SHA

If the forge denies access, stop and tell the user. Do not work around allowlists.

## Additional Information

### SonarQube

When a ready `*sonarqube*` MCP is listed:

1. Resolve `projectKey` from `.sonarlint/connectedMode.json`, then `sonar.projectKey` in project or CI config (e.g., `sonar-project.properties`), then `search_my_sonarqube_projects`.
2. `list_pull_requests` and match the ID parsed from the URI (or source branch). Pass that key as `pullRequest`, never as `branch`.
3. `get_project_quality_gate_status`, `search_sonar_issues_in_projects`, `search_security_hotspots` on that pull request.

Cite SonarQube findings that overlap the diff. If no project or analysis exists, skip with one line in the report. Do not dump the full issue list.

## Review

Apply rule precedence: **org / compliance -> Ponytail -> language rules -> core**.

If a review intent is provided by the user, judge the diff against this intent.

Look for the following (and in this order from most important to least important):

1. **Security**: injection, secrets, unsafe shell, authz gaps, etc.
2. **Bugs**: broken edge cases, regressions
3. **Tests**: Missing tests for new behavior
4. **Compliance**: Public registries or tooling that org rules forbid
5. **Ponytail**: unrequested deps, YAGNI, wrong-layer fixes
6. **Style**: Style violations

Skip, unless asked:

1. Style that already matches the project
2. Drive-by refactors and extra abstractions
4. Issues already raised in an unresolved discussion

## Report

Lead with the verdict, then a compact table. Sort findings highest severity first. Omit empty sections.

```md
## Merge Request Review

**Title**: <title>
**Link**: [<id>](<uri>)
**Verdict:** Approve | Request Changes | Comment
**Why:** <one sentence>
**Pipeline:** <status or unknown>
**Approvals:** <satisfied / missing>

### Findings

|Severity|Location|Finding|
|:-|:-|:-|
|Blocking|`path:line`|...|
|Suggestion|`path:line`|...|
|Note|`path:line`|...|
```

- **Approve**: No blocking findings.
- **Request changes**: At least one blocking finding.
- **Comment**: Suggestions only, or the review is incomplete (draft, empty diff, failing fetch).

If the diff is empty, say so in one sentence and stop.

If the user asked to post feedback, read the adapter and post **Blocking** and **Suggestion** items as inline comments when a line is known; otherwise one request-level note. Do not post **Note** items. Do not approve or merge as part of posting.
