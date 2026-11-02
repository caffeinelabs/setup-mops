# AGENTS.md

A GitHub composite Action that installs the [Mops](https://mops.one) package manager (and optional `moc`, `wasmtime`, and `pocket-ic` toolchains) with caching.

## Layout

- `action.yml` — the entire action definition (a composite action; all logic lives in its `steps`). This is the primary file to edit.
- `.github/workflows/test-self.yml` — CI that exercises the action against a matrix of `mops-version`, `wasmtime-version`, `pocket-ic-version`, and runner OS.
- `mops.toml` — pins the default `moc` toolchain version used when inputs are not provided.

## Build / test / lint

There is no build step, package manifest, or linter in this repo. The only automated check is the `test-self` workflow, which installs the action via `uses: ./` and then verifies the installed binaries. There is no local test runner; changes are validated by CI on push to `main` and on pull requests.

## Conventions and gotchas

- Inputs in `action.yml` are referenced with bracket syntax (e.g. `inputs['mops-version']`) when the name contains a hyphen.
- `action.yml` steps run `shell: sh`; keep step scripts POSIX-sh compatible.
- Third-party actions in `action.yml` are pinned to full commit SHAs with a version comment; keep new `uses:` references pinned the same way.
- The CI matrix encodes a real constraint: mops 3 rejects `pocket-ic` below `9.0.0`. Keep matrix version pairings valid when editing `test-self.yml`.
- `identity-pem` is a secret input; never hardcode a PEM. It is passed to `mops user import` only for publishing.
