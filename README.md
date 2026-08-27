# Agentic Development

This project contains rules, skills and general information about AI tools to efficiently and safely develop software with agents.

## Rules

Create symbolic links to the rules you want to use, from the repository root:

```bash
mkdir -p "${HOME}/.cursor/rules"
for FILE in rules/*.mdc; do
  LINK_NAME="${HOME}/.cursor/rules/$(basename "${FILE}")"
  [[ -e ${LINK_NAME} ]] && continue
  ln -fsn "$(realpath "${FILE}")" "${LINK_NAME}"
done
```

Shared rules (`core`, `style-ponytail`, `lang-*`) are committed in this repository. Company-specific `org-*.mdc` files are ignored (via [`.gitignore`](./.gitignore)) on purpose so a symlinked `rules/` directory can hold local overlays without committing them. See [`AGENTS.md`](./AGENTS.md) for roles and precedence.

## Skills

Create symbolic links to the skills you want to use, from the repository root:

```bash
mkdir -p "${HOME}/.cursor/skills"
for DIR in skills/*/; do
  LINK_NAME="${HOME}/.cursor/skills/$(basename "${DIR}")"
  [[ -e ${LINK_NAME} ]] && continue
  ln -fsn "$(realpath "${DIR}")" "${LINK_NAME}"
done
```

## Tools

Additional information about tools can be found in [`tools/`](./tools/).
