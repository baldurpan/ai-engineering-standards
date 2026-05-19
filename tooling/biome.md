# Biome

## Purpose

Biome replaces both ESLint and Prettier with a single fast tool. It handles formatting, linting, and import organization.

## Why Biome

- 10–100× faster than ESLint + Prettier
- Single config, single tool, single install
- Zero-conflict formatting (no Prettier vs. ESLint format fights)
- Built-in import organizer

## Setup

```bash
pnpm add --save-dev --save-exact @biomejs/biome
pnpm biome init
```

Or extend the shared config from this repo:

```json
// biome.json
{
  "$schema": "https://biomejs.dev/schemas/1.9.4/schema.json",
  "extends": ["@ai-engineering-standards/biome-config"]
}
```

## Key Commands

```bash
# Check (lint + format check, no writes)
pnpm biome check .

# Format files
pnpm biome format --write .

# Lint files
pnpm biome lint .

# Fix lint + format in one pass
pnpm biome check --write .

# CI — no writes, exit non-zero on error
pnpm biome ci .
```

## CI Integration

```yaml
# .github/workflows/ci.yml
- name: Biome check
  run: pnpm biome ci .
```

`biome ci` is equivalent to `biome check` but never writes files and always exits non-zero on any finding.

## Editor Integration

Install the Biome VS Code extension: `biomejs.biome`

Enable format on save:

```json
// .vscode/settings.json
{
  "[typescript]": { "editor.defaultFormatter": "biomejs.biome" },
  "[typescriptreact]": { "editor.defaultFormatter": "biomejs.biome" },
  "[javascript]": { "editor.defaultFormatter": "biomejs.biome" },
  "[json]": { "editor.defaultFormatter": "biomejs.biome" },
  "editor.formatOnSave": true
}
```

## Config Reference

See [packages/biome-config/biome.json](../packages/biome-config/biome.json) for the full shared config.

## Migrating from ESLint + Prettier

```bash
pnpm biome migrate eslint --include-inspired
pnpm biome migrate prettier
```

Biome can automatically migrate most ESLint and Prettier configs.

## DO NOT

- Run both Biome and Prettier on the same files — they will conflict
- Disable rules with `// biome-ignore` without a specific comment explaining why
- Skip `biome ci` in CI pipelines

## See Also

- [`ci.md`](ci.md) — `biome ci` as the gating command
- [`dependencies.md`](dependencies.md) — replacing ESLint + Prettier
- [`vite.md`](vite.md) — editor integration
