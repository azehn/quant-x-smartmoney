# ADR 0002: Move Toward API-first SaaS Architecture

Date: 2026-06-14
Status: Proposed

## Context

The product direction includes:

- A free dashboard first.
- Paid API subscriptions later.
- Permission-controlled stock-level detail.
- Hidden calculation logic.
- Potential future frontend replacement.

The current Streamlit app reads precomputed results directly from
`data/cache.pkl` and includes access to internal calculation modules.

## Decision

Add a FastAPI backend layer and gradually move Streamlit into a thin frontend
role.

Near-term:

- Keep the current repo as a monorepo prototype.
- Add API endpoints that read existing precomputed results.
- Do not change `app.py` in the first API milestone.

Long-term:

- Move calculation and API code into a private backend.
- Make Streamlit call backend APIs instead of reading local cache/calculation
  code.
- Let external API subscribers call the same backend with API keys and
  permission tiers.

## Consequences

- The project gains a clean path toward paid API access.
- Calculation logic can be hidden from frontend clients later.
- Deployment becomes more complex because backend and frontend eventually run
  separately.
- API schemas must be designed deliberately instead of exposing pickle/DataFrame
  internals.
