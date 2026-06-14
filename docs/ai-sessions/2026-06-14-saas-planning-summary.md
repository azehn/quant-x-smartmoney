# AI Session: SaaS API Planning

Date: 2026-06-14
Agent: Codex
Status: Summary

## User Goals

The user wants to evolve the current SmartMoney Streamlit dashboard into a SaaS
product.

Key goals:

- Keep dashboard free initially.
- Add paid API access later.
- Support API subscribers.
- Hide calculation logic from frontend users.
- Preserve Streamlit for now.
- Eventually support login, Stripe subscriptions, user-managed cancellation, and
  permission tiers.

## Important Decisions

- Do not split repos immediately.
- First add an API boundary inside the current repo.
- Keep existing Streamlit app stable during early API work.
- Make Streamlit a thin API client later.
- Eventually split backend and dashboard into separate repos.
- Use API key auth first.
- Add full login and Stripe later.
- Do not expose `cache.pkl` or DataFrame structures as the long-term public API
  contract.

## User Inputs Captured

- Initial users: owner plus a small circle of investors.
- Future users: paid subscribers and API consumers.
- First commercial path: free dashboard, then paid API.
- Desired data refresh options: daily, hourly, 15-minute delayed, and manual.
- API data scope: sectors, industries, AI themes, anomalies, stock-level fund
  flow, historical time series, and future result types.
- Stock-level detail should be permission controlled.
- Core algorithm should be hidden from frontend clients.
- Initial auth can be API key only.
- Later auth should support login, Stripe subscriptions, and self-service
  cancellation.
- Current deployment is Streamlit Community Cloud.
- Future deployment should be stable, inexpensive, and scalable.
- Storage can start simple with local files, then evolve to JSON/Parquet,
  SQLite, and Postgres.
- Streamlit should be kept for now.
- Public API docs are not needed immediately, but are expected later.

## Current Recommended Next Step

Create the docs structure and record the SaaS roadmap for future agents.

After that, implement FastAPI skeleton and read-only results API without changing
`app.py`.
