# Oxide playground

Use these deliberately noncompliant files to evaluate linting and formatting choices without
making the repository's normal checks fail.

## Oxlint

Edit `oxlint.ts`, then run:

```sh
pnpm exec oxlint --config playground/oxlint.json --type-aware playground/oxlint.ts
```

The command is expected to exit unsuccessfully when an enabled rule reports an error. It uses the
root `.oxlintrc.json` through the small nested configuration. Keep examples that represent
decisions you want to evaluate; this file is not production code.

## Oxfmt

Edit `oxfmt.ts`, then run:

```sh
pnpm exec oxfmt --config .oxfmtrc.json --stdin-filepath oxfmt-playground.ts < playground/oxfmt.ts
```

The formatted result is printed to the terminal and the source is left untouched, making the input
and proposed output easy to compare. The command uses the root `.oxfmtrc.json`.

Both playground files are excluded from the normal `lint`, `format`, and `format:check` commands.
