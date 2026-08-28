---
name: git-mr-review
description: Reviews a merge request.
disable-model-invocation: true
license: MIT
---

# Review a Merge Request

## Input

You MUST require a merge request URI. If none is given, you MUST ask; you MUST NOT infer the request. An optional description after the URI provides context on the review's intent.

You MUST read [`REFERENCE.md`](REFERENCE.md) first, but do not parse all linked files yet.

You MUST parse platform and identity from the URI. [`REFERENCE.md`](REFERENCE.md) provides information about platform adapters - read the suitable adapter file. You SHOULD load (in parallel where possible):

- Metadata (title, description, branches, draft, head SHA)
- File diffs (full hunks; paginate until a short page)
- Discussions and notes
- Approval / review state
- Pipeline or check status for the head SHA

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

The report opens with the merge request title, link, verdict and a short explanation of the verdict. Verdict MUST be either `APPROVE` (no blocking findings), `REQUEST CHANGES` (at least one blocking finding), or `COMMENT` (suggestions only, or the review is incomplete (draft, empty diff, failing fetch)). After the front matter, a list of findings gives details. You MUST sort findings by highest severity first and omit empty sections. Severity MUST be either `BLOCKING`, `SUGGESTION`, or `NOTE`. If the diff is empty, you MUST say so in one sentence and stop.

```md
# Merge Request Review

**Title**: <title>
**Link**: [<id>](<uri>)
**Verdict**: <VERDICT>
**Why**: <EXPLANATION OF VERDICT>
**Pipeline:** <STATUS | "UNKNOWN">

<ID>. **<SEVERITY>** (<LOCATION as `path:line` or "UNKNOWN">): <EXPLANATION>
```

If the user asked to post feedback, you MUST NOT post items with severity "NOTE". Post "BLOCKING" or "SUGGESTION" notes as inline comments when a line is known, otherwise one request-level note. You MUST NOT approve or merge as part of posting unless the user asks for it.
