# BACKLOG — Forex_IB
Updated 2026-10-26. All items below are **tasks**, not completed implementation claims.

## P0 — Source audit (must run first)
- [ ] Inspect workspace / Git history / ZIP source if present; generate honest inventory.
- [ ] Find requirements, pyproject/requirements, tests, env variables, README and launch command.
- [ ] Re-run offline tests with concrete command + output.
- [ ] Build requirements-to-code matrix (existing / missing / uncertain).
**Acceptance:** concise report listing verified behavior and blockers; avoid overwriting existing code.

## P1 — Deterministic Gold Analyst core
- [ ] Abstract MT5 client with injectable mock feed; normalize symbol/timezone.
- [ ] Fetch adequate OHLC D1/H4/H1; exclude currently-forming candle, assert sort/no duplicate timestamps.
- [ ] EMA20, EMA50, EMA200, ATR14; trend computation with transparent rules.
- [ ] Detect support/resistance as bounded zones (lower/upper), reason, timeframe, last-touched timestamp.
- [ ] Deterministic structured analysis object + human-readable Vietnamese summary.
- [ ] No trading recommendations or order placement.
- [ ] Unit tests: known indicators, insufficient history, sideways/trend, market closed, stale feed, broker suffix, irregular timestamps and lookahead.
**Acceptance:** fixture data produces stable, explainable JSON/text with no invented values.

## P2 — Telegram and scheduling
- [ ] Telegram Bot API provider; dry-run + mock provider tests.
- [ ] Message format: symbol, as-of, D1/H4/H1 trend, nearest supports/resistances, invalid/stale guard, disclaimer.
- [ ] Store idempotency in SQLite for standalone, migratable to Postgres.
- [ ] Run only once per closed H1 candle; select either Task Scheduler minute 03 or app loop, not both concurrently.
- [ ] Retry timeout/429/5xx within safe bounded limits and log failures without tokens.
- [ ] Optional chart PNG from actual OHLC, not AI-drawn chart.
- [ ] MT5 and Telegram live smoke tests with credentials in environment; record outcome.
**Acceptance:** Telegram gets at most one intended alert per new valid H1 candle; no post on stale/disconnected input.

## P3 — Service/data contracts
- [ ] Add FastAPI endpoints (proposed): `/health`, `/v1/analyses/latest`, `/internal/analyses/run`.
- [ ] Introduce Postgres + migrations for analysis_runs/zones/content/publications; use strict permissions.
- [ ] Move charts to object storage, store keys and metadata.
- [ ] Separate analysis trigger from publisher. Proper request auth/rate limiting.
**Acceptance:** service API returns deterministic payload, migrations reproducible, integration tests pass.

## P4 — Editorial workflow + website
- [ ] Deploy Payload CMS with drafts, review, approve, schedule and channel variants.
- [ ] n8n orchestration for WF-01 to WF-06; durable/idempotent events, retry/dead-letter alerts.
- [ ] Next.js site displays approved published analysis, canonical URL, risk disclaimer.
- [ ] Preview/posting controls and publishing audit trail.
**Acceptance:** editor can review/schedule/publish; website only shows eligible content; state and audit maintained.

## P5 — Social channel adapters
- [ ] Telegram adapter moved to shared publication contract.
- [ ] Facebook Page publishing through authorized Meta Graph API.
- [ ] Zalo OA publishing only if OA/product/app entitlements and applicable API capabilities allow intended post type.
- [ ] Per-channel length, media constraints, quotas, errors, idempotency, retry.
- [ ] Per-channel publication status and external_post_id recorded.
**Acceptance:** dry-run, permission tests and separately verified publish for each authorized channel.

## P6 — Operations & release
- [ ] Observability: data delay, analysis-run count, duplicate prevention, success rates, retry queues, channel auth expiry.
- [ ] Daily report workflow.
- [ ] Backup/restore, secrets rotation, operating runbook, alerts, dashboard.
- [ ] Document Windows MT5 integration and infrastructure where Linux-hosted CMS/n8n can call the data service securely.
- [ ] CI/test pipeline, deployment and rollback checklists.
**Acceptance:** outages are diagnosable; recovery and rollback documented and tested.

## Previously proposed 6-week sequencing (estimate for two developers)
W1 audit/collector+tests; W2 analyst+Telegram verification; W3 FastAPI/Postgres/n8n start; W4 CMS/website; W5 Facebook/Zalo; W6 hardening/operations.
Not a guarantee. API app approvals may extend the timeline.

## Out of scope
- MT5 order placement/auto trading/portfolio management.
- BUY/SELL signal broadcasting or entry/SL/TP recommendations.
- Unapproved auto-posting to personal Facebook accounts/private groups or unofficial Zalo routes.
- Backtest or performance marketing promises without measured historical evidence.