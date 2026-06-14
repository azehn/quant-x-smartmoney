# SaaS API Roadmap

Date: 2026-06-14
Status: Draft
Audience: Future agents and project maintainers

## Current State

Quant X SmartMoney is currently a Streamlit dashboard that loads precomputed
capital rotation results from `data/cache.pkl`.

The project already has:

- `app.py`: Streamlit dashboard.
- `smartmoney/rotation.py`: core capital rotation calculations.
- `smartmoney/loader.py`: cache/live data loader.
- `scripts/prefetch_data.py`: scheduled cache builder.
- `.github/workflows/daily_data.yml`: daily market data refresh.
- `taxonomy.yaml`: sector/theme/ticker taxonomy.
- `pyproject.toml`, `uv.lock`, `.python-version`: uv-based Python project setup.
- `README.md`, `CLAUDE.md`: developer and agent instructions.

## Product Direction

The project should evolve from a private dashboard into an API-first SaaS.

Target users:

- Initially: owner and small circle of investors.
- Later: free dashboard users.
- Later: paid API subscribers.

Commercial direction:

- Keep dashboard free at first.
- Add paid API access later.
- Eventually support user login, subscription billing, and API permissions.

Core principles:

- Hide calculation logic from frontend clients.
- Keep Streamlit as a thin frontend first.
- Move API and calculation logic toward a private backend.
- Do not expose internal pickle/DataFrame structures as the public API contract.

## Target Architecture

Near-term architecture:

```text
scripts/prefetch_data.py
    -> data/cache.pkl
    -> FastAPI backend reads cache
    -> Streamlit optionally calls FastAPI
```

Long-term architecture:

```text
Calculation Worker
    -> Result Store
        -> FastAPI Backend
            -> Streamlit Dashboard
            -> External API Subscribers
```

Future repo structure:

```text
smartmoney-backend-private
  smartmoney_core/
  smartmoney_api/
  scripts/
  data pipelines
  auth and permissions

smartmoney-dashboard
  app.py
  API client
  Streamlit UI only
```

## Milestone 1: Documentation and Planning

Goal:

Create a durable planning structure for future human and AI contributors.

Tasks:

1. Add `docs/README.md`.
2. Add `docs/plans/2026-06-14-saas-api-roadmap.md`.
3. Add `docs/adr/0001-use-uv-for-python-env.md`.
4. Add `docs/adr/0002-api-first-saas-architecture.md`.
5. Add `docs/adr/0003-split-backend-and-dashboard-repos.md`.
6. Add `docs/ai-sessions/2026-06-14-saas-planning-summary.md`.
7. Add `docs/worklog/2026-06-14-project-documentation-setup.md`.

Acceptance criteria:

- Future agents can understand the SaaS direction without reading the full chat.
- The plan explains what to do next and what not to do yet.
- No runtime behavior changes.

## Milestone 2: FastAPI Skeleton

Goal:

Add a read-only API without changing the existing Streamlit app.

Tasks:

1. Add dependencies: `fastapi`, `uvicorn`, `pydantic-settings`.
2. Create API package: `smartmoney/api/__init__.py`.
3. Create API app: `smartmoney/api/main.py`.
4. Create cache store abstraction: `smartmoney/api/store.py`.
5. Create response schemas: `smartmoney/api/schemas.py`.
6. Create auth helpers: `smartmoney/api/auth.py`.
7. Create permission helpers: `smartmoney/api/permissions.py`.
8. Add `GET /health`.
9. Add `GET /v1/meta`.
10. Make API read `data/cache.pkl` through the store abstraction.
11. Add local run docs: `uv run uvicorn smartmoney.api.main:app --reload --port 8000`.

Acceptance criteria:

- API starts locally.
- `/health` returns OK.
- `/v1/meta` returns cache metadata, updated time, ticker count, and available data groups.
- Existing Streamlit app still works unchanged.

## Milestone 3: Read-Only Results API

Goal:

Expose current dashboard data through stable JSON endpoints.

Tasks:

1. Add `GET /v1/sectors`.
2. Add `GET /v1/sectors/{etf}`.
3. Add `GET /v1/anomalies`.
4. Add `GET /v1/themes/ai`.
5. Add `GET /v1/themes/physical-ai`.
6. Add query parameters: `weighting=equal|market_cap`.
7. Add query parameters: `limit=...`.
8. Add serializers that convert internal DataFrame structures into API-safe JSON.
9. Avoid exposing raw pickle or DataFrame-specific structures.

Acceptance criteria:

- API returns useful data currently shown in Streamlit.
- Responses are JSON serializable.
- Endpoint schemas are documented internally.
- No broad permission system yet except optional dev API key.

## Milestone 4: API Key Authentication and Permission Tiers

Goal:

Prepare for paid API access by adding basic API key auth and tier checks.

Tasks:

1. Support `X-API-Key` header.
2. Store dev keys in environment variables first: `SMARTMONEY_API_KEYS`.
3. Add permission tiers: `free`, `pro`, `premium`, `internal`.
4. Restrict `free` to top-level sector summaries.
5. Let `pro` access sectors, industries, themes, anomalies, and historical time series.
6. Let `premium` access stock-level fund flow and fuller history.
7. Let `internal` access all endpoints.
8. Add smoke checks for restricted endpoints.

Acceptance criteria:

- Requests without valid API key cannot access protected endpoints.
- Endpoint-level permission checks are explicit.
- Dashboard can use an internal API key later.

## Milestone 5: Streamlit as Thin Frontend

Goal:

Make Streamlit call the API instead of reading local calculation/cache logic directly.

Tasks:

1. Add `smartmoney/client.py`.
2. Configure `SMARTMONEY_API_URL`.
3. Configure `SMARTMONEY_API_KEY`.
4. Store local secrets in `.streamlit/secrets.toml`.
5. Keep `.streamlit/secrets.toml` out of git.
6. Store production secrets in Streamlit Community Cloud secrets.
7. Gradually replace `load_all_data()` calls with API client calls.
8. Keep a temporary fallback mode to local cache during migration.

Acceptance criteria:

- Streamlit dashboard can render from API data.
- Core calculation logic is no longer needed by the dashboard frontend path.
- Dashboard repo can eventually be split from backend repo.

## Milestone 6: API-Friendly Result Store

Goal:

Stop relying on pickle as the future API contract.

Tasks:

1. Keep `data/cache.pkl` for backward compatibility.
2. Add `data/latest_meta.json`.
3. Add `data/latest_summary.json`.
4. Add `data/sector_rankings.parquet`.
5. Add `data/theme_rankings.parquet`.
6. Add `data/anomalies.parquet`.
7. Add `data/stock_flows.parquet`.
8. Update prefetch script to write both pickle and API-friendly artifacts.
9. Update API to prefer JSON/Parquet over pickle.
10. Document result schema.

Acceptance criteria:

- API no longer depends on pickle as the primary store.
- Result artifacts can be inspected without Python unpickle.
- Existing app remains compatible during migration.

## Milestone 7: Repo Split

Goal:

Separate backend/private logic from frontend/dashboard logic.

Tasks:

1. Create private backend repo.
2. Move `smartmoney/rotation.py` to backend.
3. Move `smartmoney/data.py` to backend.
4. Move `smartmoney/loader.py` to backend or replace it with API store logic.
5. Move `smartmoney/taxonomy.py` to backend.
6. Move `scripts/prefetch_data.py` to backend.
7. Move `smartmoney/api/` to backend.
8. Keep dashboard repo thin: `app.py`, `smartmoney/client.py`, Streamlit config, frontend docs.
9. Configure dashboard to call deployed backend API.
10. Remove calculation logic from dashboard repo.

Acceptance criteria:

- Dashboard repo does not contain core algorithm implementation.
- Backend repo owns data calculation and API.
- Dashboard can be public or private without exposing algorithm logic.

## Milestone 8: SaaS Foundation

Goal:

Prepare for real paid API subscription.

Tasks:

1. Add persistent user/key storage, starting with SQLite or Postgres.
2. Add API key hashing.
3. Add usage logging.
4. Add rate limiting.
5. Add admin commands for issuing and revoking keys.
6. Add Google login or email login later.
7. Add Stripe Checkout later.
8. Add Stripe Customer Portal later.
9. Add Stripe webhooks later.
10. Add subscription tier mapping later.

Acceptance criteria:

- API access can be controlled per user/key.
- Usage can be audited.
- Paid tiers can map cleanly to API permissions.

## Milestone 9: Production Deployment

Goal:

Move from prototype deployment to reliable production deployment.

Short-term deployment:

```text
Streamlit Community Cloud -> dashboard
Render/Fly/Railway -> FastAPI backend
GitHub Actions -> daily data refresh
```

Long-term deployment:

```text
Backend host -> FastAPI
Worker/cron -> data refresh
Postgres -> users, keys, subscription state
Object storage -> result artifacts
Streamlit or React -> frontend
Stripe -> billing
```

Tasks:

1. Choose backend host.
2. Add deployment config.
3. Add environment variable documentation.
4. Add health checks.
5. Add logs and alerting.
6. Add daily refresh support.
7. Add hourly refresh support.
8. Add 15-minute delayed data support if market data licensing allows it.

Acceptance criteria:

- Backend and frontend deploy independently.
- Data refresh is observable.
- Failed refreshes do not silently break the dashboard.

## Milestone 10: Public API Documentation

Goal:

Prepare for external API users.

Tasks:

1. Enable or expose FastAPI `/docs` when appropriate.
2. Add internal API examples first.
3. Add public API key setup docs later.
4. Add curl examples later.
5. Add Python examples later.
6. Add endpoint reference later.
7. Add permission tier matrix later.
8. Consider a small Python SDK later.

Acceptance criteria:

- Internal users can call the API without reading backend code.
- External docs can be published when the commercial API is ready.

## Recommended Immediate Next Step

Implement Milestone 2 and Milestone 3 without changing `app.py`.

This keeps current Streamlit functionality stable while creating the backend/API
path.
