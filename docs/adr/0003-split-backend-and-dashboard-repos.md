# ADR 0003: Split Backend and Dashboard Repos Later

Date: 2026-06-14
Status: Proposed

## Context

The user wants the core algorithm hidden and eventually wants paid API access.
Separating backend and dashboard repositories would help protect calculation
logic and make the dashboard a replaceable client.

Splitting immediately would add operational overhead before the API boundary is
proven.

## Decision

Keep the current repo as a monorepo prototype first.

Split into separate backend and dashboard repos only after:

- FastAPI endpoints exist.
- Streamlit can consume API responses.
- API schemas are stable enough to serve as a frontend/backend contract.
- Permission and auth boundaries are clear.

## Future Repo Shape

Backend private repo:

- Core calculation modules.
- Data fetching and prefetch jobs.
- API service.
- Auth and permission logic.
- Result store integrations.

Dashboard repo:

- Streamlit UI.
- API client.
- UI configuration.
- Frontend-only docs.

## Consequences

- Near-term development remains simpler.
- The future split will be lower risk because the API contract will already
  exist.
- Agents should avoid moving code across repos until the API/client boundary is
  implemented and validated.
