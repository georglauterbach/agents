---
name: git-review-mr
description: Reviews a merge request or pull request from a URI.
disable-model-invocation: true
license: MIT
---

# Review Merge Request

Review a merge request from an URI. You MUST report findings in chat. You MUST NOT post, merge, implement, restart, approve, deny, comment or push anything unless the user explicitly asks.

## Input

You MUST require a merge request URI. If none is given, you MUST ask; you MUST NOT infer the request.

You MUST parse platform and identity from the URI, choose a platform and read the adapter file:

|Platform|URI shape|Identity|Adapter File|MCP Name Matches|
|:-|:-|:-|:-|:-|
|GitLab|`https://<host>/<project_path>/-/merge_requests/<iid>`|`project_path`, `iid`|[`gitlab.md`](gitlab.md)|`*gitlab*`|
|GitHub|`https://<host>/<owner>/<repo>/pull/<number>`|`owner/repo`, `number`|[`github.md`](github.md)|`*github*`|
|Gitea|`https://<host>/<owner>/<repo>/pulls/<number>`|`owner/repo`, `number`|[`gitea.md`](gitea.md)|`*gitea*`|

When you encounter an unknown platform, you MUST tell the user and exit.

An optional description after the URI provides context on the review's intent.

## Fetch

You MUST use the tools laid out in the platform adapter:

1. MCP Servers: if a ready server matches the platform, you MUST use it for all forge API operations; you MUST NOT use a CLI, `curl`, or a browser while that MCP is available.
2. Else, Platform CLI: if installed, you MUST use it.
3. Else: you MUST stop and ask whether to proceed with basic tools (WebFetch, `curl`); you MUST NOT scrape unprompted.

You SHOULD load, in parallel where possible:

- Metadata (title, description, branches, draft, head SHA)
- File diffs (full hunks; paginate until a short page)
- Discussions and notes
- Approval / review state
- Pipeline or check status for the head SHA

If the forge denies access, you MUST stop and tell the user. You MUST NOT work around allowlists.

## Review

You MUST apply rule precedence: **org / compliance → Ponytail → language rules → core**.

If a review intent is provided by the user, you MUST judge the diff against this intent.

You MUST look for the following (and in this order from most important to least important):

1. **Security**: injection, secrets, unsafe shell, authz gaps, etc.
2. **Bugs**: broken edge cases, regressions
3. **Tests**: Missing tests for new behavior
4. **Compliance**: Public registries or tooling that org rules forbid
5. **Ponytail**: unrequested deps, YAGNI, wrong-layer fixes
6. **Style**: Style violations

You MUST skip the following, unless asked:

1. Style that already matches the project
2. Drive-by refactors and extra abstractions
3. Issues already raised in an unresolved discussion

## Report

The report opens with the merge request title, link, verdict and a short explanation of the verdict. Verdict must be either `APPROVE` (no blocking findings), `REQUEST CHANGES` (at least one blocking finding), or `COMMENT` (suggestions only, or the review is incomplete (draft, empty diff, failing fetch)). After the front matter, a list of findings gives details. You MUST sort findings highest severity first and omit empty sections. If the diff is empty, you MUST say so in one sentence and stop.

```md
# Merge Request Review

**Title**: <title>
**Link**: [<id>](<uri>)
**Verdict**: <VERDICT>
**Why**: <EXPLANATION OF VERDICT>
**Pipeline:** <STATUS | "UNKNOWN">

<ID>. **<SEVERITY ∈ (BLOCKING|SUGGESTION|NOTE)>** (<LOCATION as `path:line`>): <EXPLANATION>
```

If the user asked to post feedback, you MUST read the adapter and post **BLOCKING** and **SUGGESTION** items as inline comments when a line is known; otherwise one request-level note. You MUST NOT post **Note** items. You MUST NOT approve or merge as part of posting unless the user asks for it.
