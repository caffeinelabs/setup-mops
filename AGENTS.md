# AGENTS.md

A composite GitHub Action that installs the [Mops](https://mops.one) package manager (and optional Motoko toolchain binaries) with caching, for use in other workflows.

## Layout

- `action.yml` — the action definition; all behavior lives here as composite `run` steps.
- `mops.toml` — toolchain pins (`moc` version) consumed by this repo and by the action's defaults.
- `.github/workflows/test-self.yml` — CI that self-tests `action.yml`.

## Build / test / lint

There is no compile or lint step. The only automated check is the self-test workflow in `.github/workflows/test-self.yml`, which runs on push to `main` and on pull requests. It uses the action (`uses: ./`) across a matrix of `mops-version`, `wasmtime-version`, `pocket-ic-version`, and runner OS, then verifies each tool resolves and reports a version. There is no local test runner; validate changes by editing `action.yml` and letting CI exercise it, mirroring the steps in that workflow.

## Conventions and gotchas

- `action.yml` steps run under `shell: sh` (and `shell: composite` constraints); keep step scripts POSIX-sh compatible, not bash-specific.
- Third-party actions in `action.yml` are pinned to full commit SHAs with a `# vX.Y.Z` comment. Preserve this pinning style when adding or bumping actions.
- Inputs: `mops-version`, `moc-version`, `wasmtime-version`, `pocket-ic-version`, `identity-pem`. A version-shaped `mops-version` (e.g. `1` or `1.2.3`) installs from onchain storage; anything else installs `ic-mops` from npm.
- `identity-pem` is a secret for publishing; never hardcode it — pass it via GitHub Secrets.
- The action requires Node.js (installed as step 1) and targets Node 22.
- CI runs on both `ubuntu-latest` and `macos-latest`; changes must work on both. The cache path includes both `~/.cache/mops` (Linux) and `~/Library/Caches/mops` (macOS).
