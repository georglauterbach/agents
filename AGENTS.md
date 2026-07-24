# agents - Agent Guide

This project provides rules to efficiently and safely develop software with agents.

## Repository Layout

| Path       | Purpose                                                 |
| :--------- | :------------------------------------------------------ |
| `.github/` | GitHub-related content like CI/CD, issues, etc.         |
| `rules/`   | contains Agent "[rules](https://cursor.com/docs/rules)" |

Only rules explicitly allowed in `.gitignore` are committed and updated in this project. This allows for company-specific rules when symlinking the `rules/` directory without them accidentally being committed.
