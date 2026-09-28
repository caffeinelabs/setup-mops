# AGENTS.md

This repository is a GitHub composite Action that installs the [Mops](https://mops.one) package manager (with caching) into a workflow.

## Layout

The action's entire logic lives in `action.yml` (a composite action). There is no application source tree, build system, or test suite—the repo is the action definition plus documentation.

- `action.yml` — the composite action: its `steps` are the implementation, and its `inputs` are the public API.
- `README.md` — usage and the input reference. Keep it in sync with `inputs` in `action.yml`.

## Build / test / lint / format

There are no build, lint, or format commands—no `package.json`, `Makefile`, or equivalent exists.

The only test is CI (`.github/workflows/test-self.yml`), which runs the action against itself on `push` to `main` and on every `pull_request`. It uses `uses: ./` to exercise `action.yml`, then verifies the installed tools. It runs a matrix over:

- `mops-version`: `0.41.1`, `latest`
- `wasmtime-version`: `24.0.0`
- `pocket-ic-version`: `5.0.0`
- `runner`: `ubuntu-latest`, `macos-latest`

There is no way to run this test locally as a script; it validates the action end-to-end on GitHub runners.

## Conventions and gotchas

- **Pin third-party actions by commit SHA.** Existing steps in `action.yml` and the workflow pin `actions/*` to a full commit SHA with the version as a trailing comment (e.g. `# v4.4.0`). Follow this pattern for any new action reference.
- All `inputs` are optional and each corresponding step is guarded by an `if:` condition; a new input should follow the same conditional-step pattern.
- `identity-pem` is a secret. It must be passed via GitHub Secrets, never inlined.
- Steps use `shell: sh` (POSIX), not bash. Keep step scripts POSIX-compatible.
- The default `moc`, `wasmtime`, and `pocket-ic` versions are read from a consumer's `mops.toml`; the `mops.toml` in this repo (`moc = "0.10.0"`) is only the fixture used by CI/self-testing.
