# AGENTS.md

This repository is a composite GitHub Action (`dfinity/setup-mops`) that installs [Mops](https://mops.one), the Motoko package manager, with caching for toolchain and packages.

## Layout

- `action.yml` — the action definition. All logic lives here as composite `run` steps; there is no separate source to build.
- `.github/workflows/test-self.yml` — CI that exercises `action.yml` against a matrix of mops/wasmtime/pocket-ic versions on Ubuntu and macOS.
- `mops.toml` — declares the default `moc` toolchain version used when the action's `moc-version` input is not set.

## Build / test / lint

There is no build or lint step for this action; it is plain YAML. The only automated check is the self-test workflow (`.github/workflows/test-self.yml`), which runs on push to `main` and on pull requests. It installs the action via `uses: ./` and verifies that `mops`, `moc`, `wasmtime`, and `pocket-ic` resolve and report versions. Validate changes to `action.yml` by running that workflow (or its steps) rather than a local command.

## Conventions and gotchas

- Action input names and defaults in `action.yml` are a public contract documented in `README.md`; keep the two in sync when changing inputs.
- GitHub Action `uses:` references are pinned to commit SHAs with a trailing version comment (e.g. `# v4.4.0`). Preserve this pinning style when adding or updating steps.
- The `identity-pem` input is a secret; never hardcode or echo it. It is imported with `mops user import` only for publishing.
- The self-test matrix encodes a compatibility constraint: mops 3.x rejects pocket-ic below 9.0.0, so `mops-version` and `pocket-ic-version` pairs must stay compatible when edited.
- `mops-version` accepts either a full/partial semver (installed via the mops install script) or any other value (installed from npm as `ic-mops`); the `Set env var` step in `action.yml` branches on this.
