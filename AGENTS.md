# AGENTS.md

A composite GitHub Action that installs [Mops](https://mops.one) (the Motoko package manager) with caching of packages and toolchain.

## Layout

- `action.yml` — the composite action definition; its `steps` are the entire implementation.
- `.github/workflows/test-self.yml` — CI that exercises `action.yml` across a matrix of versions and runners.

## Build / test / lint

There is no build step, package manager, or local test runner in this repo. The action is validated only by CI.

- CI (`test-self.yml`) runs on pushes to `main` and on pull requests. It invokes the action via `uses: ./` across a matrix of `mops-version`, `wasmtime-version`, `pocket-ic-version`, and runners (`ubuntu-latest`, `macos-latest`), then checks the installed tool versions and binary paths.
- To test a change, run the action from a workflow (e.g. `uses: ./`) rather than any local command.

## Conventions

- GitHub Action dependencies (e.g. `actions/setup-node`, `actions/cache`) are pinned to commit SHAs with a trailing version comment. Keep that pinning style when editing `action.yml` and `.github/workflows/`.
- Inputs mirror Mops concepts: `moc-version`, `wasmtime-version`, and `pocket-ic-version` map to `mops toolchain use <tool> <version>`.
- Node.js is installed at version 22 inside the action.
- `identity-pem` is a secret; never hardcode a PEM value — pass it via GitHub Secrets.
