RAYAN FX v3.0 — Android-first web app with serverless backend

WHAT IS INCLUDED
- Live forex quote and OHLC candles from Twelve Data REST API (polling every 30/60/120 seconds, not tick-by-tick streaming).
- AI chart screenshot analysis using OpenAI Responses API image input.
- Multi-timeframe EMA/RSI/structure heuristics, WAIT safety gates, local journal and risk calculator.
- Netlify Functions proxy so provider keys are never shipped to the browser.

IMPORTANT: NOT A 10/10 VERIFIED TRADING SYSTEM
This is a professional-oriented prototype. No profitability or signal accuracy has been established. The analysis engine is heuristic. External prices may be delayed and can differ from the user's MT5 broker. It does not execute trades.

DEPLOYMENT
This app requires a host that supports serverless functions. Netlify Drop for static files alone is not enough. Deploy the whole folder through a Git-connected Netlify site, or use a compatible serverless host.
Phone-only route:
1. Create a private GitHub repository using your phone browser.
2. Upload all files/folders from this ZIP, preserving paths.
3. In Netlify, add a new site by importing that GitHub repository.
4. Set the publish directory to "." and functions directory to "netlify/functions" if asked; netlify.toml contains these settings.
5. In Netlify site settings > Environment variables, add:
   TWELVE_DATA_API_KEY = your Twelve Data API key
   OPENAI_API_KEY = your OpenAI API key
   RAYAN_ACCESS_PIN = a unique random PIN/passphrase (at least 6 characters)
   OPENAI_VISION_MODEL = gpt-4.1-mini (optional; leave unset if unavailable for your account)
   Ensure variables are available to Functions. Never put API keys in index.html, GitHub, or a message to anyone.
6. Trigger a new deploy after adding/changing variables.
7. Open the deployed HTTPS URL in Chrome Android > menu > Install app / Add to Home screen.
8. In RAYAN Settings, enter the RAYAN_ACCESS_PIN and save it for the browser session. Test server. Then connect live market.
9. Upload a chart screenshot in the AI Screenshot tab.

PROVIDER NOTES
- Twelve Data API access, real-time forex entitlements, request quotas and delays depend on your account/plan. Check their current pricing and supported symbols before paying.
- This implementation uses REST polling at 30/60/120-second intervals to avoid exposing a market-data key in the browser. It is not a WebSocket tick feed. The API plan must permit the three requests per refresh; bid/ask spread is not supplied by the selected endpoint.
- OpenAI API billing is separate from ChatGPT subscriptions. Image analysis API calls may incur charges.
- Do not send screenshots containing personal account identifiers or balances.

SECURITY
- API keys remain server-side in Netlify environment variables.
- The app requires a shared access PIN for its functions. This is a basic private-app gate, not a full account system. Keep the site private/unlisted, use a strong unique PIN, and rotate it if exposed.
- The functions validate supported symbols, timeframes, image MIME type and approximate upload size.
- Public deployment can still be abused if the PIN leaks. Monitor provider usage and rotate keys/PIN when needed.

LIMITATIONS / SAFETY
- No MT5 direct connection and no automatic order placement.
- Live quote/candles are from Twelve Data, not the broker's exact feed.
- AI can misread small chart labels and cannot infer hidden order flow or future prices.
- News filter is a manual checkbox; no live economic calendar is included in this build.
- Position sizing requires exact broker-specific contract value; verify lot size, tick value, account currency and stop-distance calculations.
- Demo-test extensively. Do not risk money you cannot afford to lose.
