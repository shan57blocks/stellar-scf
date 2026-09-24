# Paycashless

Paycashless is a payments and money-management app for small and medium businesses in Nigeria. One merchant account covers getting paid (POS terminals, online checkout, pay-by-bank-transfer links, virtual bank accounts), invoices, payouts to staff and suppliers, and cash-flow reports. It launched in January 2026 and reports $600K+ in merchant volume, of which $200K+ came from crypto-funded payments. The SCF #44 grant puts Stellar underneath the app as the layer where money settles and where each merchant's wallet lives. The SCF project page shows $78K awarded and $23,400 paid so far.

## What SCF #44 pays them to build

Total $78K, in three tranches (payment stages).

- **Tranche 1, MVP: $23,400 (6 weeks)**
  - Test environments for Privy and NEAR Intents, repo and CI setup, and a technical design. ($7,800)
  - Settlement engine and embedded wallets: every merchant gets a Stellar account with a USDC trustline (permission to hold USDC), and balances are tracked live. ($9,600)
  - First crypto payment (for example ETH, or USDT on Tron) turned into USDC on the merchant's Stellar account through NEAR Intents, on testnet. ($6,000)
- **Tranche 2, Testnet: $26,400 (16 weeks)**
  - NEAR Intents consolidation from at least three source chains. ($6,400)
  - Cross-border and cash payouts through MoneyGram Access (SEP-1, SEP-10, SEP-24, SEP-31), including cash pickup via a USDC-to-XLM DEX trade. ($10,000)
  - Opt-in yield on idle USDC through Blend v2, plus an analytics pipeline built from on-chain data. ($6,000)
  - One dashboard showing the naira (NGN) and USDC balances, and a closed beta with 10+ merchants. ($4,000)
- **Tranche 3, Mainnet: $28,200 (24 weeks)**
  - Mainnet deployment and end-to-end testing, with a deployment runbook. ($8,200)
  - NEAR Intents hardening: a treasury backstop for unfilled intents, and more source chains. ($4,000)
  - Professional user testing with real merchants. ($10,000)
  - Public docs, a developer guide and a public reference repo for the Anchor, NEAR Intents and Blend integrations. ($3,000)
  - Production monitoring and alerting, with a 99.5% uptime target. ($3,000)

## How it works

- **Settlement engine.** This is the core new part. It links each merchant to a Stellar address and picks the path for every credit and debit. It writes Stellar transaction hashes into Paycashless's own ledger and sends events to the analytics service. It watches accounts through Horizon's live stream (Horizon is Stellar's public data API) and checks balances against the ledger every 5 minutes.
- **Wallets (Privy).** Privy is an embedded-wallet service. Merchants who log in through Privy get a Stellar key split between Privy and their login provider (this is called MPC), so they never see a seed phrase. Merchants who keep the normal Paycashless login get an account whose keys Paycashless holds in its encrypted key vault. Either way, a USDC trustline is added when the account is created.
- **Getting paid.** Crypto payments on other chains go through NEAR Intents. NEAR Intents is a network of "solvers" that swap the funds and deliver native USDC to the merchant's Stellar account. Naira payments can be converted to USDC, if the merchant opts in, through the Anchor Platform deposit flow (SEP-24). SEPs are Stellar's shared standards for how apps talk to anchors (regulated on/off-ramp companies).
- **Paying out.** Payouts inside Nigeria stay on the existing licensed bank rail. Cross-border payouts use MoneyGram Access: the app finds MoneyGram (SEP-1), logs in by signing a challenge (SEP-10), then pays through SEP-24 (interactive) or SEP-31 (automatic). For cash pickup, USDC is swapped to XLM on the Stellar DEX first.
- **Yield.** This is off by default. When a merchant turns it on, USDC above a reserve they set is supplied to Blend v2 lending pools (Soroban contracts) and withdrawn before any payout.
- **Analytics.** Events from the settlement engine and Horizon feed a real-time reporting pipeline.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recnS4XjkADmGtmDb | submission.md | Awarded $78K. This is the team's only SCF submission. |
| Technical Architecture Document | architecture | https://docs.google.com/document/d/1ogK_j2kVoB3tLFPgYxis8aqf16dM4rI7-TB1BRa2vCQ/edit?tab=t.0 | architecture.txt | Plain-text export; system diagram not included |
| Paycashless developer docs | docs site | http://docs.paycashless.com | docs-site.md | Page index, home and use cases. Covers the current naira API (checkout, payouts, virtual accounts, invoices). No Stellar content yet. |
| OpenAPI spec | spec | https://docs.paycashless.com/api-reference/openapi.json | — | Current (non-Stellar) API |
| qpi.js README | docs site | https://github.com/paycashless/qpi.js | qpi-js-readme.md | Library that encodes and decodes QR payment-request data. Not Stellar code. |
| GitHub org | docs site | https://github.com/paycashless | — | Two repos: qpi.js and qpi-integration-guides (which is empty) |
| Website | docs site | https://paycashless.com | — | Opens (200) |
| Arkham wallet view (crypto volume evidence) | demo | Arkham "visualizer/entity" link in the submission (very long URL; see submission page) | — | intel.arkm.com returns 403 to scripts; not checked |

## Gaps

- No public Stellar code yet. The public reference repo is a Tranche 3 deliverable.
- The docs site does not cover Stellar settlement, payouts or yield yet. Updating it is a Tranche 3 deliverable.
- There is no pitch deck, whitepaper, demo video or audit linked.
- The architecture doc copy is text only. Its "Full System Diagram" image is missing.
- The Arkham evidence link could not be opened with scripts (HTTP 403).
