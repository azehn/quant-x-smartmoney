# AGENTS.md

## Project Commands

Use uv for all Python commands.

```bash
uv sync --locked
uv run streamlit run app.py
uv run python scripts/prefetch_data.py
uv run python cli.py
```

## Architecture

architecture/API/SaaS tasks should read docs/README.md, latest roadmap, and relevant ADRs.

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

## Cost-sensitive mode

- Minimize repository exploration.
- Do not scan the whole repo unless the user explicitly asks.
- Only inspect files the user explicitly mentions, or direct dependencies required by those files.
- Default to a narrow file set, but for cross-module, architecture, API, data pipeline, or SaaS tasks, read the relevant docs and directly related modules without stopping at an arbitrary file count. If exploration grows broad, summarize what you have learned and explain why more files are needed before continuing.
- Do not run the full test suite unless the user explicitly approves.
- Prefer targeted tests for the changed file or module.
- Do not print long logs or full file contents.
- If command output exceeds 100 lines, summarize the relevant error and stop.
- Make the smallest correct patch.
- Avoid unrelated refactors.
- Keep the final summary under 10 bullets.

## Workflow

1. Identify the smallest relevant file set.
2. Explain the intended change briefly before editing.
3. Make the minimal patch.
4. Run only targeted checks.
5. Summarize changed files and test result.
