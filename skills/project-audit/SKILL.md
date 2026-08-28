---
name: project-audit
description: Audits a project for consistency, correctness, soundness and style problems and reports findings without changing anything.
disable-model-invocation: true
license: MIT
---

# Audit Project

Audit a project and report findings in chat. The audit itself changes nothing: you MUST NOT edit, commit, push, or open a request while auditing. When findings exist, you close by asking whether to fix them.

## Input

With no argument, the subject is the whole working tree of the current repository. An optional path narrows the audit to that subtree. An optional description sets the intent (e.g. "focus on the shell scripts"); you MUST judge the repository against it in addition to the dimensions below.

## Orientation

Before reading source, you MUST learn what the project is and which checks it configures:

1. `.project_index.yaml`, when it exists — the module map, read first
2. `AGENTS.md`, `README.md`, and `CONTRIBUTING.md` — the stated intent you audit the tree against
3. `.editorconfig` and tool configuration (`pyproject.toml`, `Cargo.toml`, `cspell.config.yaml`, `.gitignore`, CI workflows) — the project's own rules

Stated intent is evidence, not truth: a `README` that describes a layout the tree no longer has is itself a consistency finding.

## Scope

You MUST enumerate candidates with `git ls-files --cached --others --exclude-standard` so ignored paths stay out while uncommitted new files stay in, then drop generated output, vendored dependencies, lock files, and binaries. The subject is the working tree, not a commit; plain `git ls-files` would hide the newest files, which are the likeliest to be wrong.

When the tree is too large to read in one pass, you MUST audit the highest-risk parts first (entry points, security-relevant code, whatever the intent names) and MUST state in the report which parts you did not read. An audit that silently skipped half the repository is worse than one that admits it.

## Running Checks

The project's own tooling finds mechanical defects faster and more reliably than reading does, but you MUST NOT execute anything unasked.

1. Derive the candidate commands from the `Tooling` tables of the applicable `lang-*.mdc` rules and from what the project actually configures (`cargo clippy`, `shellcheck`, `cspell`, `yamllint`, ...).
2. You MUST list the exact commands you intend to run and ask once, then wait.
3. You MUST run only what the user approved, and only read-only checks. You MUST NOT install tooling, write files, or run a formatter in write mode.

When the user declines, or a tool is missing, the audit proceeds as a reading pass and the report says so.

## Dimensions

You MUST apply rule precedence: **org / compliance → Ponytail → language rules → core**. You MUST audit in this order, most important first:

1. **Security**: secrets in tracked files, unsanitized external input, unsafe shell, missing authorization, public registries where org rules forbid them
2. **Correctness and soundness**: broken edge cases, unhandled errors, wrong invariants, blocking calls on async paths, unreachable or dead code
3. **Consistency**: documentation, configuration, and code disagreeing — tables or lists that no longer match the directory, broken relative links, referenced files that do not exist, `.gitignore` or `.editorconfig` or spell-check drift, content duplicated where one copy will rot
4. **Tests**: non-trivial logic with no runnable check behind it (`style-ponytail.mdc`)
5. **Ponytail**: unrequested abstractions, avoidable dependencies, boilerplate, logic at the wrong layer
6. **Style**: violations of the project's own coherent style or of its language rules

You MUST skip, unless asked:

- style that the project applies consistently, even where you would choose differently
- refactors and abstractions that only restructure working code
- generated files

Repeated instances of one defect belong in a single finding with a count and the worst location, not one row each.

## Report

You MUST lead with the verdict, then a findings table sorted highest severity first.

```md
## Project Audit

**Project:** <name> at <short SHA>
**Scope:** <whole tree | subtree>, <n> files
**Checks run:** <commands, or "none">
**Verdict:** Healthy | Needs Attention | Unsound

|#|Severity|Location|Finding|
|:-|:-|:-|:-|
|1|Blocking|`path:line`|...|
|2|Suggestion|`path:line`|...|
|3|Note|`path:line`|...|
```

- **Blocking**: a security or correctness defect
- **Suggestion**: consistency, a missing check, a Ponytail violation
- **Note**: style

Verdict is **Unsound** with a security defect or a correctness defect in the project's main path, **Needs Attention** with any other finding, **Healthy** with none. Every finding MUST cite `path:line` and keep its number, which is how the user selects fixes.

## Fix

With no findings, you MUST say the repository is clean in one sentence and stop; you MUST NOT ask.

Otherwise you MUST ask, once, whether to fix, and then wait:

1. Fix blocking findings only
2. Fix everything reported
3. Fix specific findings (the user names the numbers)
4. Nothing, report only

Until the answer arrives, you MUST NOT edit, commit, or push. Once the user chooses to fix, the selected findings are the work order: you MUST fix root causes rather than the reported symptom, you MUST NOT fix findings the user did not select, and you MUST re-run whichever approved checks cover the files you changed.
