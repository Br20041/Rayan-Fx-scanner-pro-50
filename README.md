# RAYAN FX SCANNER PRO 5.0 — ULTIMATE EDITION

Android-first Progressive Web App (PWA) + Node.js backend for evidence-led Forex research and manual MT5 execution. Configured defaults: Exness Standard, USD account, KES as a display reference, intraday and swing workflows, 0.25% maximum risk per trade, independent Twelve Data OHLC feed, manual broker-price verification. No broker login and no auto-trading.

## What is actually implemented

### 1. Multi-timeframe candidate engine
- H4, H1 and M15 OHLC analysis.
- EMA 20/50/200 regime alignment.
- RSI(14), MACD histogram, ATR(14), ADX-style directional-strength filter.
- Confirmed local swing pivots and close-break BOS heuristic.
- Last-candle direction/body filter, ATR-percent guardrails and per-symbol spread/ATR gate.
- Transparent rule score and explicit reasons/risks. The score is **not** a win probability or calibrated confidence.
- Strict final gate: score ≥85, aligned direction and a user-supplied verified per-symbol spread. Without a spread value, final result remains WAIT.
- Candidate review notifications from cloud scan; cloud cannot know the live Exness spread and therefore never promotes a cloud candidate to executable BUY/SELL.

### 2. Risk control
- Hard validation: risk must be greater than zero and no more than 0.25% of USD equity.
- Calculates risk budget and volume from user-supplied stop pips and verified USD pip value per 1.00 lot.
- Rounds volume down to the entered/assumed lot step in the risk panel (current UI assumes 0.01; verify your exact Exness instrument specification).
- Backend risk endpoint supports lot step, min/max lot and estimated round-turn cost.
- If the minimum lot would exceed the budget, the correct decision is no trade.

### 3. Research / backtesting
- CSV OHLC upload and backend EMA20/EMA50 crossover baseline.
- Next-bar-open entry; configurable ATR stop, reward/risk, spread, slippage, commission, pip value and pip size.
- Separate chronological training and final 30% out-of-sample summaries.
- Conservative same-candle collision handling: stop is assumed hit before target when both are touched.
- Reports trade count, win rate, net R, profit factor, max drawdown and ending equity.
- This research baseline is deliberately labelled **not the same as the live multi-timeframe candidate engine**. It does not establish a strategy edge.

### 4. Operational tools
- AI MT5 screenshot review through server-side OpenAI Responses API; key stays on server; screenshot max 8 MB.
- Optional Finnhub economic calendar endpoint.
- Five-minute server scheduler for up to 20 watchlist symbols; cloud scan requests H4/H1/M15 candle series. Check provider request limits before enabling a large watchlist.
- Web Push / Android Chrome notification framework using VAPID. Alerts are review prompts, not orders.
- Bearer-token protection for private API endpoints, API rate limiting, file-size limits, security headers, persistent JSON storage for one personal instance.
- `/healthz` health endpoint and `/api/status` capability flags.

## Methodology coverage — what is not falsely claimed

The broad requested methodology includes market structure, price action, SMC/ICT, supply/demand, indicators, multi-timeframe analysis, volume/order flow, fundamentals, currency strength, sessions, volatility, patterns, execution, risk, trade management, psychology, backtesting, journaling, statistics and regime analysis.

PRO 5.0 implements a deterministic subset: EMA trend regime, RSI/MACD/ATR/ADX-style filters, simple confirmed swing-break heuristics, candle body, spread/volatility gating, risk sizing, an EMA baseline backtest, economic calendar connector, screenshot review and manual checklist. The following remain **manual, partial, or not implemented**: full ICT/SMC interpretation; institutional order flow/real volume; automated supply/demand zones; robust FVG/OB/mitigation logic; cross-pair currency-strength/correlation matrix; news sentiment and central-bank policy analysis; session/liquidity model; complete chart-pattern classifier; broker tick/bid-ask feed; swap-aware portfolio risk; trade journaling/analytics dashboard; calibrated probability model; full walk-forward optimization and multi-regime validation. Do not treat a heuristic label as proof that these checks passed.

## Android-only deployment

1. Download the ZIP and extract it on Android (Files app or an archive utility).
2. Create a **private** GitHub repository. Upload the contents of this project folder (including `server.js`, `lib/`, `public/`, `package.json`, `render.yaml`, and `README.md`). Never upload `.env`, API keys, access tokens or `data.json`.
3. Create/connect a Render Node web service from that private repository. Render can use `render.yaml` as the blueprint. The included configuration uses a persistent disk at `/var/data`.
4. Add server environment variables in Render: `TWELVE_DATA_API_KEY` (cloud scanning), `OPENAI_API_KEY` (optional screenshot AI), `FINNHUB_API_KEY` (optional calendar), `VAPID_PUBLIC_KEY`, `VAPID_PRIVATE_KEY`, `VAPID_SUBJECT` (optional push). Render generates `APP_ACCESS_TOKEN`; keep it secret.
5. Generate VAPID keys locally using `npx web-push generate-vapid-keys` in a trusted Node environment, or omit push keys until you are ready. Never paste secrets into a public repository or chat.
6. Deploy and wait for build success. Open the HTTPS Render URL on Android Chrome. Install from Chrome menu → Add to Home screen / Install app.
7. Open Cloud → Set/change server token and enter the Render `APP_ACCESS_TOKEN`. Save no more than 20 symbols. Enable notifications only after VAPID is configured.
8. For manual scanner, enter your Twelve Data key in Settings (it is stored in this browser's local storage) and use a spread map in quote-price units, e.g. `EUR/USD=0.00012, USD/JPY=0.012`. These are examples only, not current spreads. Use the actual live spread from Exness MT5 for each pair.

## Environment variables

See `.env.example`. Keep credentials private. API provider plans, endpoint access, pricing, rate limits and terms can change; confirm them with the providers before use.

## Validation performed in this source package

- `node --check` for server and core modules.
- Frontend inline JavaScript syntax check.
- `node --test`: unit tests for the 0.25% risk cap, lot rounding, no-spread WAIT gate and backtest input/OOS structure.
- No production hosting deployment, live provider API call, Android push delivery test, AI billing test, broker quote reconciliation or profitability validation has been performed in this build environment.

## Important limits / safe use

- This is a single-user personal deployment, not a hardened multi-user SaaS. JSON persistence assumes one server instance and a persistent disk. Back up the disk. Do not horizontally scale this version.
- The cloud watchlist is capped at 20 pairs per configuration to reduce provider quota consumption. It is not an automatic scan of every Forex instrument simultaneously. The manual scanner can be given a different symbol list.
- Twelve Data candle history is not the same as Exness executable bid/ask prices. Verify symbol mapping, bid/ask, spread, pip/tick value, contract size, volume step, margin, commissions, swap and conversion on Exness MT5 before every order.
- A candle-derived spread field is not auto-fetched; user enters it. The scanner also rejects candle sets older than 12 hours on H4, 4 hours on H1, or 90 minutes on M15 (timestamps requested in UTC). An incorrect spread entry can invalidate the filter.
- Cloud scan sends candidate-review alerts based on directional rule score. It has no Exness quote and cannot issue a fully gated trade signal.
- Calendar event time/impact and coverage depend on Finnhub plan and feed. Confirm timezone and official release details.
- AI screenshot analysis can misread chart labels, scales, indicators and structure. It is an assistant reviewer, not an authoritative price source.
- Backtesting is a simple EMA crossover research baseline, not the live MTF engine. CSV quality, timeframe, costs and pip-size inputs matter. It does not model all historical variable spreads, gaps, swaps, liquidity, broker fills, market impact or parameter overfitting. Out-of-sample results are not a guarantee.
- No profitability or win-rate claim is made. Validate with clean historical data, realistic costs, walk-forward tests, demo forward testing and a sufficiently large trade sample before risking real funds.
- No Exness/MT5 account integration, credential storage, trade placement or automatic execution exists.

## Developer commands

```bash
npm install
npm test
npm start
```

The service requires Node.js 20 or later. The web UI and API are served from the same HTTPS origin.
