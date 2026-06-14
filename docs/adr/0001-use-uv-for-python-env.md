# ADR 0001: Use uv for Python Environment

Date: 2026-06-14
Status: Accepted

## Context

The project needs reproducible local development and scheduled data refreshes.
It is an application/dashboard rather than a reusable library package.

The project previously used `requirements.txt` only. It now has uv project files.

## Decision

Use uv as the canonical Python project environment.

Track these files in git:

- `.python-version`
- `pyproject.toml`
- `uv.lock`

Keep `.venv/` untracked.

Use `uv sync --locked` for reproducible setup and CI installs.

Use `uv run ...` for local project commands.

## Consequences

- Local dev, CI, and scheduled jobs use the same dependency lockfile.
- New contributors and agents have a clearer setup path.
- `requirements.txt` can remain temporarily for compatibility, but uv is the
  preferred path.
