# AGENTS.md — Codex instructions for Forex_IB

## Project identity
Forex_IB = XAUUSD market commentary + editorial multi-channel distribution platform.
Primary language for end-user market commentary: Vietnamese.
Source of price data: MetaTrader 5 terminal connected to Exness.
First deliverable: hourly Gold AI Analyst Bot to Telegram.
Second deliverable: CMS/website/social publishing workflows.

## Non-negotiable constraints
1. DO NOT execute trades or add order placement interfaces. No MT5 `order_send` or BUY/SELL advice engine in MVP.
2. Analyze trends and technical price zones only, using **closed candles** from D1/H4/H1; no chart hallucination.
3. Reuse existing repo / ZIP if provided: inspect file tree, deps and test output before modifying anything.
4. Never claim a reported historical ZIP or offline tests are locally verified without re-running tests.
5. Keep deterministic indicators/zones in Python. n8n orchestrates and must not own financial computations.
6. Every scheduled H1 run requires idempotent key e.g. `(symbol, last_closed_h1_timestamp, analysis_version)`.
7. Never post content with stale/invalid data; failure should be visible via logs and metrics.
8. Keep tokens/credentials out of git, screenshots, logs, tests and prompts. Provide `.env.example`.
9. Show timestamps with UTC internally and local timezone only in presentation.
10. Public posts include a short market-data / non-investment-advice disclaimer.
11. Do not implement unauthorized channel posting or bypass Meta/Zalo platform permissions. Approved API connections only.
12. Start with a `dry-run`/mock-data path that does not require a live Exness account or channel token.

## Coding style preferences
- Python 3.11+ typed modules with small composable services and pytest.
- Validate external inputs with Pydantic (when introducing FastAPI); explicit timezone-aware datetime.
- Keep `data_source`, `indicators`, `zone_detection`, `analysis`, `report`, `chart`, `publisher`, `storage` separate.
- Keep per-channel formatting/credential/rate-limit logic isolated behind adapter contracts.
- Avoid ungrounded `confidence` fields. If produced, document their computation.
- Unit tests must use synthetic OHLC fixtures; smoke tests require explicit credentials and network.
- Prefer minimal useful diffs with tests. Report paths changed and how tested.

## Source status
Old file name (not bundled/verified): `Gold_AI_Analyst_MT5_Telegram_MVP.zip`.
Old behavior was described as Python+MT5+Telegram+SQLite, `--dry-run/--once/--loop`, EMA20/50/200+ATR14+swing zones and optional LLM.
Historical assistant report: 7/7 offline tests passed. Do not adopt as current test status.

## First Codex move
1. Inventory the repository. Report found files and matching features.
2. Run any tests, or explicitly report blockers. Never fabricate test results.
3. Map existing modules to requirements in `PROJECT_CONTEXT.md`.
4. Recommend shortest executable milestone: deterministic analysis from fixtures -> dry-run formatted Telegram message -> Exness live smoke test -> Telegram live smoke test.
5. Only after MVP1 is verified, introduce FastAPI/DB/n8n/CMS in separate small increments.