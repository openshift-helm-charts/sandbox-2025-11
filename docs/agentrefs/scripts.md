# Python Scripts

All paths below are repository-root-relative unless noted otherwise.

Python source for the CI scripts invoked by the workflows in [`.github/`](../../.github/). Layout:

- `scripts/src/` - Python packages implementing certification logic, called in GitHub Actions workflows.

## Setup and validation

Everything runs through `scripts/Makefile`, which uses [uv](https://docs.astral.sh/uv/) and the committed `scripts/uv.lock`. Two environments are used: `scripts/.venv` (the CI scripts package plus test dependencies) and `scripts/venv.lint` (Black and Ruff only). Run these commands from `scripts/`:

```bash
make install    # Installs CI scripts (editable) and test deps into .venv
make format     # Installs lint tools before running Black
make lint       # Installs lint tools before running Ruff
```

Code style is checked with Ruff (configured in `scripts/ruff.toml`); run `make format` before submitting.

Unit tests for `scripts/src/` run with pytest. To run them locally:

```bash
make install
uv run --group test pytest src/
```

## Conventions

- CI logic in `scripts/` is released into the charts and stage repositories - keep public interfaces (entry points, function signatures used by workflows) backwards compatible where practical.

## Cautions

- Changes to `scripts/src/` trigger the sandbox-based Behave tests on PRs from approvers (see [Functional tests](functional-tests.md)).
- See [Python tooling README](../../scripts/README.md) for cluster token/secret management (`scripts/get-secrets`) - never commit credentials.
