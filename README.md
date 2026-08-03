# Agentic Development

This project contains rules to efficiently and safely develop software with agents.

Create symbolic links to the rules you want to use:

```bash
mkdir -p "${HOME}/.cursor/rules"
for FILE in rules/*.mdc; do
  ln -fs "${FILE}" "${HOME}/.cursor/rules/$(basename "${FILE}")"
done
```

Shared rules (`core`, `style-ponytail`, `lang-*`) are committed in this repository. Company-specific `org-*.mdc` files are ignored (via [`.gitignore`](./.gitignore)) on purpose so a symlinked `rules/` directory can hold local overlays without committing them. See [`AGENTS.md`](./AGENTS.md) for roles and precedence.
