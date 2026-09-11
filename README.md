# x402 QR Global Growth

Cloud Itonami Bot profile for expanding **x402.nexus** through qualified QR payment pilots. `.itonami/profile.edn` describes work and carries no credentials or grants.

Merchant displays QR → customer approves Base USDC in a wallet browser → Nexus records settlement → authenticated terminal claims a stable issuance command → terminal acknowledges its durable receipt. Base Sepolia is test money. Printer adapters and mainnet merchant qualification are separate acceptance gates.

Implementation: https://github.com/network-awai/nexus-x402 (access controlled). Public discovery: https://x402.nexus/qr/capabilities. A reachable endpoint does not prove customer demand or physical issuance.

## Run

New operator? Walk `docs/operator-quickstart.md` — one observation end to end, with the exit codes and the two failure modes.

`kbb --backend sci scripts/observe.cljk` performs one bounded GET and records status, body digest, timestamp, and uncertainty in `reports/latest-observation.edn`. No payments or prospect messages. `kbb --backend sci test/profile_test.cljk` validates this repository's contract.

On a CLI release supporting repo profiles, `itonami profile explain` shows selection. `itonami chat --profile x402-qr-growth` requires a destination Bot. See `data/runtime.edn` for measured provisioning state; cloning does not schedule a Bot.

Deliverables: country readiness with official sources; merchant/terminal pipeline; Japanese and English onboarding drafts; pilot conversion and reliability reports; a decision memo to expand, improve, pause, or obtain evidence.
