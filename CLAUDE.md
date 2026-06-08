# CLAUDE.md

## Project Commands

Use uv for all Python commands.

```bash
uv sync --locked
uv run streamlit run app.py
uv run python scripts/prefetch_data.py
uv run python cli.py
```

## Architecture

- `app.py`: Streamlit dashboard with Overview, Sector Breakdown, AI Themes, and Physical AI tabs.
- `smartmoney/rotation.py`: capital rotation metrics, industry aggregation, rankings, and anomaly detection.
- `smartmoney/loader.py`: shared Streamlit data loader; reads `data/cache.pkl` first and falls back to live yfinance fetches.
- `smartmoney/data.py`: yfinance OHLCV download, parquet cache support, and daily-to-weekly resampling.
- `smartmoney/taxonomy.py`: loads `taxonomy.yaml` and exposes helper lookups.
- `smartmoney/report.py`: static HTML report generation for the CLI flow.
- `taxonomy.yaml`: source of truth for sectors, industries, themes, and tickers.
- `scripts/prefetch_data.py`: scheduled cache generation script used by GitHub Actions.

## Important Notes

- Keep `uv.lock` committed.
- Update `taxonomy.yaml` for ticker, sector, industry, or theme changes.
- Do not edit `data/cache.pkl` manually; rebuild it with `uv run python scripts/prefetch_data.py`.
- `market_cap` weighting currently uses dollar volume, `close * volume`, not true market cap.
- The preferred local dashboard command is `uv run streamlit run app.py`.
- The daily production data workflow is `.github/workflows/daily_data.yml`.
