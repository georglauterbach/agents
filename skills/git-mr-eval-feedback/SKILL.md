---
name: git-mr-eval-feedback
description: Evaluate and apply merge request feedback.
disable-model-invocation: true
license: MIT
---

# Evaluate and Apply Merge Request Feedback

## Input

You MUST require a feedback source: a merge request URI or pasted text. If none is given, you MUST ask; you MUST NOT invent items from chat history.

You MUST read [`REFERENCE.md`](REFERENCE.md) first, but do not parse all linked files yet.

The subject is the working tree of the current repository, not a prior conversation. An optional path narrows which files may change.

When the source is a URI, you MUST parse platform and identity from it. [`REFERENCE.md`](REFERENCE.md) provides information about platform adapters - read the suitable adapter file. You SHOULD load (in parallel where possible):

- Discussions & notes (paginate until a short page)

For each cited path, you MUST read the working-tree file. You SHOULD load the merge request diff only for those paths. If the item list is empty, you MUST say so in one sentence and stop.

If the source is pasted text or a review report, that text is the item list. You MUST NOT fetch a merge request unless a URI is given.

You MUST NOT re-review the merge request.

## Review

You MUST apply rule precedence: **org / compliance → Ponytail → language rules → core**.

You MUST judge each item against the working tree, not against the reviewer's wording alone.

The following classifications are possible:

1. **KEEP**: valuable
    - every remaining item that names a concrete change
2. **SKIP**: not important
    - style that already matches the project
    - a drive-by refactor or extra abstraction
    - a nit with no security, correctness, or test effect
3. **DISCARD**: wrong
    - contradicts the code or the stated intent
    - would violate a higher-precedence rule
    - is already applied in the working tree

When two APPLY items conflict, you MUST stop and ask; you MUST NOT pick a side.

## Report

The report opens with the source and the number of items kept, discarded and skipped. After the front matter, a list of all review items gives more details. You MUST sort findings: kept items first, then skipped items, then discarded items.

```md
# Feedback Review

**Source**: <URI | Pasted>
**Kept**: <n>
**Skipped**: <n>
**Discarded**: <n>

<ID>. [<ACTION>] <ITEM>: <REASON>
```

## Apply

If the user wants you to apply feedback after the report phase, you start fixing all APPLY items. You MUST NOT apply DISCARD or SKIP items. You MUST fix the root cause the items point at, not only the cited line. You MUST NOT expand into unrequested refactors. You MUST NOT edit files outside an optional path the user named. After edits, you SHOULD run the project's configured checks on the files you changed when those tools are already in use.
