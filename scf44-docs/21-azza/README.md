# Azza

Azza is a payments app that runs inside WhatsApp. People in Africa (Nigeria, Kenya, Ghana, South Africa and more) chat with a bot to buy, hold, send and cash out stablecoins (crypto tokens pegged to the US dollar, such as USDC). The team says it has 12,000+ users and 12 fiat corridors (a "corridor" is a currency route, for example Naira to Cedi). The SCF #44 grant ($89.0K) pays Azza to add Stellar to this app in two ways: a USDC savings product that earns interest, and payouts to the US, Europe and the UK.

## What SCF #44 pays them to build

- **Tranche 1 - MVP / testnet foundation ($28,500):** Stellar treasury account and USDC transfers on testnet ($4,500); Blend v2 deposit/withdraw service on testnet ($6,000); Bridge sandbox payout adapter ($5,000); staging environment and test harness ($3,500); project kickoff and setup ($9,500).
- **Tranche 2 - core flows end to end ($28,500):** WhatsApp savings deposit with balance and APY display ($6,000); savings withdrawal to USDC or local money ($5,000); Bridge inside Azza's payout router for USD/EUR/GBP ($7,000); international onramp (USD/EUR/GBP in, USDC out) ($3,500); yield accounting service ($4,500); risk monitoring and reconciliation ($2,500).
- **Tranche 3 - mainnet ($38,000):** Blend v2 savings live on mainnet ($8,000); Bridge payouts live on mainnet ($8,500); full QA ($7,000); production dashboards and alerts ($5,000); documentation, SCF reporting and handover ($3,500).

## How it works

- **The user side stays WhatsApp.** A user types "Save $50" or "Withdraw my savings". The bot talks to Azza's back-end services.
- **Savings.** Azza moves the user's USDC to one shared Azza treasury account on Stellar. From there it deposits into **Blend v2**, a lending app built as Soroban smart contracts (programs that run on Stellar). Borrowers pay interest, which is the yield. Blend sees Azza as one big lender, so Azza keeps each user's share in its own database and checks daily that the numbers match the on-chain position. Azza holds the keys (custodial).
- **Payouts.** Azza already has a "Provider Router" that picks a payout partner per corridor. **Bridge** (a Stripe-owned stablecoin payments company) is added as one more partner for USD, EUR and GBP. Bridge converts the money, settles it over Stellar and pays a bank (ACH, SEPA, SWIFT). Bridge also converts incoming USD/EUR/GBP into USDC.
- **Stellar plumbing.** New services: a Stellar Wallet Service (signs transactions), a Horizon Listener (watches the Stellar network through Horizon, Stellar's public API, to confirm payments), a Yield Accounting Service, and a Savings Service.
- **Safety checks.** KYC (identity check) required before saving; retries with idempotency keys (so a retry never pays twice); deposits paused if the Blend pool is too full of loans; Bridge webhooks (callback messages) are signature-checked.

No Soroban contracts of their own are planned; Azza calls Blend's existing contracts.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission "Scaling Azza Across Africa" | requirements | https://communityfund.stellar.org/submissions/rec2zu1jNgJIoO9K0 | submission.md | Awarded $89.0K |
| Technical proposal "USDC Savings & Global Payout Rails on Stellar" | architecture | https://ivory-tory-46.tiiny.site | architecture.md, architecture.pdf | 16-page PDF, transcribed. States $95,000 total incl. a $9,500 kickoff tranche; submission says $89.0K |
| SCF project page | earlier SCF submission | https://communityfund.stellar.org/project/recCDXy34vVpROziQ | — | Lists this award plus one other submission (below) |
| Other SCF submission "Azza" ($110,000, status Submitted) | earlier SCF submission | https://communityfund.stellar.org/submissions/recLrZxSN59MTBRvj | — | Page loads but shows no content without login; not readable |
| Website | docs site | https://www.useazza.com | — | Marketing site; no Stellar content found |
| Azza blog | blog | https://www.useazza.com/blog | — | General crypto/FAQ posts; no Stellar posts |
| Dune on-chain volume dashboard | demo | https://dune.com/devjosh/useazza-onchain-volume | — | Traction evidence |
| Repo: azza-fx-management-service | repo | https://github.com/Blocverse01/azza-fx-management-service | — | NestJS + React FX service; README is a generic template; has server/API_DOCUMENTATION.md. No Stellar code |
| Repo: azza-business-whatsapp-bot | repo | https://github.com/Blocverse01/azza-business-whatsapp-bot | — | Bot README (product pitch) and docs/blockchain-config.md. No Stellar code |
| Repo: azza-defi | repo | https://github.com/Blocverse01/azza-defi | — | Public, last pushed 2024-12; not checked in depth |
| Demo video (from bot README) | demo | https://www.loom.com/share/d74a937909c44bf69d0545ad939a1d9f | — | Older WhatsApp bot demo |
| Azza Insights on Medium (from bot README) | blog | https://medium.com/azza-insights | — | Returns 403 to automated fetch; not verified |
| Launch posts (Ghana, Argentina, new rails) | blog | https://x.com/useazza/status/1962500323206893606 | — | X posts cited as traction |

## Gaps

- No public repository contains Stellar code yet.
- The second SCF submission (recLrZxSN59MTBRvj, $110,000) could not be read without logging in.
- The budget in the technical PDF ($95,000) differs from the awarded submission ($89.0K); the Tranche 3 lists also differ (the PDF adds beta cohort and incentive items).
- The Medium "documentation" link returned 403 and was not opened. The bot README writes it as `hhttps://...` (typo).
- The ten "Link1-Link10" recognition links in the submission text had no URLs in the published copy.
