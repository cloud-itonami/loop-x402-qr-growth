# Initial release observation — 2026-09-06

Product implementation merged: https://github.com/network-awai/nexus-x402/pull/31
Product main: `8ab2f10b080caf131df341b680e043caed48eb2b`
Cloudflare deployment version: `c733883b-86b8-409a-9f8a-bf1013feeaf6`

Observed live: `/health`, `/catalog`, `/qr/capabilities`, and `/qr/checkout.js` HTTP 200; unconfigured merchant order creation HTTP 401. Public capability body/digest recorded in latest-observation.edn.

Verified locally: existing full suite 173 tests/725 assertions; final QR contract 4 tests/27 assertions. Actual workerd SQLite/RPC with simulated facilitator and printer: 12 concurrent requests at each create/pay/claim boundary; duplicate settlement transaction rejected; payer terms/nonce binding, terminal isolation, repeat acknowledgment, and uncertain settlement tested. Browser displayed Test ticket, 0.01 USDC, Base Sepolia TEST MONEY, and missing-wallet guidance.

Current upstream facilitator `/supported` advertises Base mainnet exact, but not Base Sepolia (observed 2026-09-06). Sepolia is an implemented order contract and local test rail; live Sepolia settlement requires a compatible configured facilitator. No merchant config was enabled, no real money moved, no physical machine was connected, no country was admitted, and external demand is unmeasured.

Next pilot inputs: exact kiosk make/model and SDK; merchant receiving wallet; selected jurisdiction and operating entity; compatible facilitator; terminal secret provisioning; physical crash/paper-out test. Until qualified, prepare artifacts and conduct source-backed research only. This report is not permission for outreach or financial operations.
