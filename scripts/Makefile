# This is what we pass to git ls-files.
LS_FILES_INPUT_STR ?= 'src/*.py'

# The virtualenv containing the uv-managed lint tools. Deliberately NOT the
# project environment: `uv sync` is exact, so syncing --only-group lint into
# the shared env would prune the project and its test dependencies back out
# of an env that `make install` had just populated.
# Matched by the `venv.*/` rule in .gitignore.
VENV_LINT ?= venv.lint
UV_LINT_ENV = UV_PROJECT_ENVIRONMENT=$(VENV_LINT)

.PHONY: default
default: format lint

# Install the project and its test dependencies.
# CI sets UV_PROJECT_ENVIRONMENT=<workspace>/ve1 so the existing
# `ve1/bin/<command>` call sites keep working. Locally, unset, uv builds
# scripts/.venv.
.PHONY: install
install:
	uv sync --locked --group test

# Install ONLY black and ruff -- not the project, not its runtime deps.
.PHONY: install.lint
install.lint:
	$(UV_LINT_ENV) uv sync --locked --only-group lint

# The same as format, but will throw a non-zero exit code
# if the formatter had to make changes.
.PHONY: ci.format
ci.format: format
	git diff --exit-code

.PHONY: format
format: install.lint
	$(UV_LINT_ENV) uv run --only-group lint black \
		--verbose \
		$$(git ls-files $(LS_FILES_INPUT_STR))

.PHONY: lint
lint: install.lint
	$(UV_LINT_ENV) uv run --only-group lint ruff \
		check \
		$$(git ls-files $(LS_FILES_INPUT_STR))
