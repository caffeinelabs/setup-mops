# AGENTS.md

A reusable GitHub composite action (`setup-mops`) that installs the [Mops](https://mops.one) package manager, with caching, in a GitHub Actions workflow.

## Layout

The entire action lives in a few root files; there is no separate source directory.

- `action.yml` — the composite action definition (all install logic lives here as shell steps).
- `mops.toml` — declares the default Motoko toolchain (`moc`) version used when a caller does not override it.
- `.github/workflows/test-self.yml` — CI that exercises `action.yml` against a matrix of versions and runners.

## Build, test, lint, format

There is no compile/build step and no separate lint or format tooling in this repo.

The only test is CI in `.github/workflows/test-self.yml`, which runs on `push` to `main` and on `pull_request`. It uses the action from the repo root (`uses: ./`) to install a matrix of `mops`, `wasmtime`, and `pocket-ic` versions on `ubuntu-latest` and `macos-latest`, then verifies each tool by checking its path and version (e.g. `mops --version`, `mops toolchain bin moc`).

To validate a change to `action.yml`, run this workflow (open a PR or push to `main`); it is the authoritative check.

## Conventions and gotchas

- Steps in `action.yml` must set `shell:` explicitly (the composite steps use `sh`); GitHub requires this for composite `run` steps.
- Callers reference the action by tag (e.g. `dfinity/setup-mops@v1`), so keep `action.yml` backward compatible within a major version.
- Inputs (`mops-version`, `moc-version`, `wasmtime-version`, `pocket-ic-version`, `identity-pem`) are documented in both `action.yml` and `README.md`; update both when changing them.
- `identity-pem` is a secret input; never hardcode a PEM value and keep it flowing only from GitHub Secrets.
- Third-party actions used inside `action.yml` (`actions/setup-node`, `actions/cache`) are pinned to commit SHAs with a version comment; keep new action references pinned the same way.
