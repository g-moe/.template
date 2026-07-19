# Monorepo template

A pnpm and TypeScript monorepo template for applications and shared packages.

## Requirements

- nvm
- Corepack

The required Node version is pinned in `.nvmrc`, and the pnpm version is pinned in `package.json`.

## Setup

```sh
nvm install
nvm use
./setup.sh
```

`nvm use` selects the pinned Node version in the current shell. `./setup.sh` verifies that version, activates the pinned pnpm version through Corepack, and installs dependencies with the frozen lockfile. After the first setup, `pnpm setup` reruns the same repository setup.

## Structure

- `apps/` contains deployable applications and services.
- `packages/` contains reusable workspace packages and shared configuration.
- `scripts/` contains repository setup and maintenance automation.

Each app or package owns its manifest, source, build, typecheck, and test configuration. Every workspace must define `build`, `typecheck`, `test`, and `coverage` scripts; `pnpm validate:repo` enforces this contract. The `dev` and `test:integration` scripts are optional. Playwright end-to-end tests are discovered across the repository by the root configuration.

## Commands

- `pnpm dev` runs workspace development commands in parallel.
- `pnpm build` builds every workspace that defines `build`.
- `pnpm typecheck` type-checks every workspace that defines `typecheck`.
- `pnpm test` tests every workspace that defines `test`.
- `pnpm coverage` runs coverage in every workspace that defines `coverage`.
- `pnpm test:integration` runs integration tests in every workspace that defines `test:integration`.
- `pnpm test:e2e` runs all `*.e2e.test.ts` files with Playwright in Chromium.
- `pnpm test:e2e:install` installs the local Chromium binary required by Playwright.
- `pnpm lint` and `pnpm format:check` verify repository files without changing them.
- `pnpm lint:fix` and `pnpm format` apply automatic fixes.
- `pnpm loc` prints a language-level line count for Git-tracked files.
- `pnpm check` runs workspace-contract, lint, format, typecheck, test, and Knip checks.
- `pnpm validate` runs the same complete gate as CI: `check`, coverage, integration tests, build, and end-to-end tests.

## Adding a workspace

Create a directory under `apps/` or `packages/` with a private or publishable `package.json`. Pin dependency versions exactly and define the four required workspace scripts. Config-only workspaces should use explicit no-op scripts so the exception remains visible.

TypeScript workspaces should extend the narrowest applicable configuration:

- `tsconfig.node.json` for Node applications and non-emitting Node packages.
- `tsconfig.browser.json` for browser applications built by a bundler.
- `tsconfig.library.json` for emitting Node libraries; set package-local `rootDir` and `outDir`.

Vitest workspaces should use the shared `vitest.config.ts`, which enforces 90% branch, function, line, and statement coverage. Test filenames determine their suite and environment:

- `*.node.test.ts` is a unit test that runs in Node.
- `*.jsdom.test.ts` is a unit test that runs in jsdom.
- `*.integration.test.ts` is an integration test that runs in Node.
- `*.e2e.test.ts` is an end-to-end test that runs in Playwright, not Vitest.

A typical workspace defines these scripts:

```json
{
	"scripts": {
		"coverage": "vitest run --coverage --config ../../vitest.config.ts --project node --project jsdom",
		"test": "vitest run --config ../../vitest.config.ts --project node --project jsdom",
		"test:integration": "vitest run --config ../../vitest.config.ts --project integration"
	}
}
```

After initial repository setup, run `pnpm test:e2e:install` once to install Chromium. CI installs Chromium and its operating-system dependencies automatically.

After cloning this template, rename the root package and replace this README title with the new project name.
