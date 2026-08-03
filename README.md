# Agentic Development

This project contains rules to efficiently and safely develop software with agents.

Copy the rules from [`rules/`](./rules/) into `~/.cursor/rules/`, or symlink them:

```sh
mkdir -p ~/.cursor/rules
ln -s /path/to/agents/rules/*.mdc ~/.cursor/rules/
```

Linking the files individually keeps any rules you already have in `~/.cursor/rules/`; re-run the command after adding a rule. If that directory does not exist yet, you can symlink the whole `rules/` directory to it instead.

Shared rules (`core`, `style-ponytail`, `lang-*`) are committed. Company-specific `org-*.mdc` files are gitignored on purpose so a symlinked `rules/` directory can hold local overlays without committing them. See [`AGENTS.md`](./AGENTS.md) for roles and precedence.
