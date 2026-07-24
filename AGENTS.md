# agents - Agent Guide

This project provides rules to efficiently and safely develop software with agents.

## Repository Layout

| Path       | Purpose                                         |
| :--------- | :---------------------------------------------- |
| `.github/` | GitHub-related content like CI/CD, issues, etc. |
| `rules/`   | contains Agent "rules" [1]                      |

Only rules explicitly allowed in `.gitignore` are committed and updated in this project. This allows for company-specific rules when symlinking the `rules/` directory without them accidentally being committed.

[//]: # (Links)

[1]: https://cursor.com/docs/rules
[2]: https://cursor.com/docs/skills
