# agents - Agent Guide

This project contains rules to efficiently and safely develop software with agents.

## Repository Layout

|Path|Purpose|
|:-|:-|
|`.github/`|GitHub-related content like CI/CD, issues, etc.|
|`rules/`|contains Agent "[rules](https://cursor.com/docs/rules)"|
|`tools/`|contains information about different tools (like the Cursor CLI)|

## Rules

Copy or symlink files from [`rules/`](./rules/) into `~/.cursor/rules/` (or a project's `.cursor/rules/`).

|File|Apply|Role|
|:-|:-|:-|
|`core.mdc`|always|Precedence, dialogue, language and security defaults|
|`style-ponytail.mdc`|always|Minimal diffs, YAGNI, no unrequested abstractions|
|`lang-python.mdc`|globs|Python idioms and tooling|
|`lang-rust.mdc`|globs|Rust idioms and tooling|
|`lang-shell.mdc`|globs|Shell idioms and tooling|
|`org-*.mdc`|local|Company overlays (not committed; see below)|

### Precedence

When rules conflict: **org / compliance → Ponytail → language rules → core**. See the Precedence section in `core.mdc`.

### Company overlays

Only rules explicitly allowlisted in [`.gitignore`](./.gitignore) are committed. Files matching `org-*.mdc` (for example `org-rohde_schwarz.mdc`) are expected to stay local when you symlink `rules/` into Cursor. Do not add them to the allowlist unless they are intentionally shared.
