---
name: project-index
description: Builds or rebuilds `.project-index.yaml`, a repository-root map of modules, key files, tags and summaries that lets agents locate relevant files without a full-tree search. Use when the user asks for a project index, when an existing index is stale after a refactor or rename, or when work in a large unfamiliar repository keeps requiring broad searches.
license: MIT
---

# Build a Project Index

Write `.project-index.yaml` at the repository root. Agents read it first when locating files (see the `project-index.mdc` rule); it is a map, never a specification.

## When Not To Build One

You MUST NOT create an index when it costs more than it saves:

- the repository is small enough to grasp by listing it (roughly under 30 source files)
- the tree is flat with self-describing names and no subsystems
- the working copy is a throwaway clone, a scratch directory, or otherwise not yours to add files to

In those cases, you MUST say so in one sentence and stop.

## Scope

You MUST index what the repository actually ships: source, tests, and behavior-defining configuration. In documentation- or configuration-shaped repositories, that is the Markdown or configuration itself.

You MUST NOT index generated output, vendored dependencies, lock files, binaries, secrets, or `.project-index.yaml` itself. Enumerate candidates with `git ls-files` so ignored paths stay out.

The index MUST stay readable in one pass, roughly 200 lines. When a repository is too large for that, index only the subtree in play and say so in the top-level `summary`.

## Schema

You MUST index **modules and their key files only**; you MUST NOT list every file. Broad search covers the long tail, and an exhaustive list goes stale faster than it helps.

```yaml
---
schema: 1
generated_at_commit: 4f2a1c9
summary: Service that issues and validates API credentials; start at src/main.py
modules:
  - paths: [src/auth/, tests/auth/]
    summary: Authentication, sessions, and tokens
    tags: [auth, jwt, session]
    key_files:
      - path: src/auth/jwt.py
        summary: Issues and verifies JWTs
      - path: src/auth/session.py
        summary: Session store and cookie lifecycle
```

Required: `schema`, `generated_at_commit` (short `git rev-parse --short HEAD`), a top-level `summary` naming what the project is and where to start, and for each module `paths`, `summary`, `tags`, `key_files`.

- `paths` is always a list, even with one entry, and directories carry a trailing slash. A module MAY span several roots, which is how a subsystem and its separate test tree stay together.
- `tags` belong on modules only. They carry concerns that no directory name shows, such as `telemetry` spread across several modules. Key files are discriminated by their `summary`.
- summaries MUST NOT contain a colon followed by a space unless the whole value is quoted; a plain YAML scalar containing `: ` does not parse.

## Procedure

You MUST:

1. Enumerate tracked files with `git ls-files` and drop everything out of scope.
2. Group the remainder into modules: one per coherent subsystem, listing every root it occupies in `paths`. Aim for a handful of modules, not one per file.
3. Give each module a one-line factual `summary` and reusable lowercase, hyphenated `tags`.
4. Under `key_files`, list only the few files an agent should read first in that module, each with a one-line `summary`. A module with more than about five key files is really two modules.
5. Reuse a small shared tag vocabulary across modules (`cli`, `http`, `parse`, `test`) instead of unique phrases; tags are how cross-cutting work is found.
6. Write the top-level `summary`: what the project is, and the one file or module to start from.
7. Sort modules by their first path, `key_files` by `path`, and `tags` alphabetically, so regeneration produces a minimal diff.
8. Stamp `generated_at_commit` last, from the commit you indexed.
9. The YAML file MUST start with `---`

You MUST keep summaries factual and MUST NOT copy secrets or large code excerpts into the index.

## Rebuilding

`git diff --name-only <generated_at_commit>..HEAD` shows what moved since the index was written; you SHOULD read it first to see which modules are actually affected. You MUST then regenerate the whole file rather than patch around a stale one, and re-stamp `generated_at_commit`.

## Committing

You SHOULD commit the index so other agents benefit. When the repository is not yours, keep it local through `.git/info/exclude`, which is untracked, rather than `.gitignore`, which is not. You MUST ask the user when ownership is unclear.
