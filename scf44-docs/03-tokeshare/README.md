# Tokeshare

Tokeshare lets people buy small shares of real things that earn money: rental homes, restaurants, vehicles, and gold. Each share is a token (a digital unit recorded on a blockchain). The money the asset earns (rent, restaurant sales) is paid out to share holders in USDC (a digital dollar). The platform already runs on Polygon, Ethereum and Base. The SCF #44 grant pays to bring it to Stellar, aimed mainly at investors in Latin America (Dominican Republic, Argentina, Colombia), including people who have never used crypto.

## What SCF #44 pays them to build

Total award: $133.0K, in three tranches (payment stages).

- **Tranche 1 — MVP (first working version)**
  - 1.1 Soroban RWA infrastructure: a sale contract for many assets and a compliant share token. $26,200. Due Aug 1, 2026.
  - 1.2 Investor sign-up: Stellar Wallets Kit (Lobstr, Freighter, xBull) plus Privy (log in with email, no seed phrase). $12,400. Due Aug 22, 2026.
- **Tranche 2 — Testnet (Stellar's test network)**
  - 2.1 Revenue payout engine plus data layer (event indexer and API for dashboards). $20,800. Due Sep 12, 2026.
  - 2.2 Cash and bank access: MoneyGram (cash in/out), alfredpay (local bank payouts), Stellar Disbursement Platform (bulk payouts). $24,600. Due Oct 3, 2026.
- **Tranche 3 — Mainnet (the live network)**
  - 3.1 Mainnet launch: CCTP (Circle's tool to move USDC from Ethereum/Polygon), Soroswap and Aquarius trading, first real asset and first real payout. $34,800. Due Nov 10, 2026.
  - 3.2 Production platform on tokeshare.co, public metrics dashboard, technical docs. $14,200. Due Nov 24, 2026.

## How it works

"Soroban" is Stellar's smart contract system (programs that run on the blockchain). "SEP-41" is Stellar's standard shape for a token contract.

The architecture doc describes four layers:

1. **Entry points** — Privy (email login), Stellar Wallets Kit (existing wallets), CCTP (USDC from other chains).
2. **Soroban contracts** — a sale contract, one RWA token per asset, and a revenue distribution engine. All paid in Circle's USDC.
3. **Trading** — Soroswap pools and Aquarius routing for resale of shares.
4. **Cash** — MoneyGram, alfredpay and the Stellar Disbursement Platform.

What the public contracts repo (`tokeshare-stellar-contracts`) actually contains today:

- **RWA token**: a SEP-41 token built from OpenZeppelin Stellar parts. Every transfer must pass five checks: not paused, both sides on the allowlist (approved list), neither side blocked, not frozen, enough unfrozen balance. Supply is capped. No KYC (identity check) module on-chain; who gets on the allowlist is decided off-chain.
- **Sale**: sells shares at a fixed price for USDC. It also buys shares back from holders, paid from the sale's own USDC balance, minus a fee. The repo README says there is no secondary market (no order book).
- **Distributor**: one contract pays each asset's monthly USDC revenue. A server computes the holder list and publishes only a Merkle root (a short fingerprint of the whole list) on-chain. The operator pushes payouts in batches; holders who were skipped can claim later. Each payout is checked against the root.
- **Deployed**: one mainnet asset (TFW_001, a quad vehicle, 100 shares at 50 USDC) and one testnet asset (TRES, "Angel Cœur Caribe" real estate, 23,000 shares at 10 USDC).

The app repo (`tokeshare`) is the Next.js website. It holds the Stellar plans: Privy and Wallets Kit share one signing interface, and asset contract addresses are kept in a config file (no database).

Note: the repo design differs in places from the architecture PDF. The PDF has the sale contract deploy each token (`create_asset`) and a snapshot-based `claim()`. The repo deploys tokens with scripts and uses Merkle-verified push and claim payouts.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recFXz7DtcwZ9JzFJ | submission.md | Tranches, budget, completion criteria |
| SCF project page | earlier SCF submission | https://communityfund.stellar.org/project/recrQIsbEkrVeaNPi | — | Lists only SCF #44; no earlier rounds |
| Technical Architecture (PDF) | architecture | https://drive.google.com/file/d/1nU1CmwaCQZplYEVGsnmKAi6jh5NilPSb/view?usp=drivesdk | architecture.pdf, architecture.txt | 4 layers, 3 contracts, 8 integrations, security review (STRIDE), stack |
| Contracts repo README | spec | https://github.com/bim-finance-org/tokeshare-stellar-contracts | contracts-readme.md | Token, sale, distributor; deployed addresses; measured fees |
| Contracts ARCHITECTURE.md | architecture | https://github.com/bim-finance-org/tokeshare-stellar-contracts/blob/master/docs/ARCHITECTURE.md | contracts-architecture.md | Why token and sale are split; transfer checks |
| ADDING-AN-ASSET.md | docs | https://github.com/bim-finance-org/tokeshare-stellar-contracts/blob/master/docs/ADDING-AN-ASSET.md | — | Step-by-step guide to tokenize an asset |
| OPERATIONS.md | docs | https://github.com/bim-finance-org/tokeshare-stellar-contracts/blob/master/docs/OPERATIONS.md | — | Running a live asset: compliance, pricing, buyback |
| Tranche 1 plan | spec | https://github.com/bim-finance-org/tokeshare/blob/master/docs/stellar/tranche-1-plan.md | tranche-1-plan.md | Work plan for deliverables 1.1 and 1.2 |
| Tranche 2 plan | spec | https://github.com/bim-finance-org/tokeshare/blob/master/docs/stellar/tranche-2-plan.md | tranche-2-plan.md | In French. Distributor, indexer, API plan |
| RWA token repo brief | spec | https://github.com/bim-finance-org/tokeshare/blob/master/docs/stellar/rwa-token-repo-brief.md | rwa-token-repo-brief.md | What the token repo must build |
| RWA token repo plan | spec | https://github.com/bim-finance-org/tokeshare/blob/master/docs/stellar/rwa-token-repo-plan.md | — | Setup plan for the token repo |
| App repo | docs | https://github.com/bim-finance-org/tokeshare | — | README is the default Next.js text |
| Website | docs site | https://tokeshare.co/ | — | No docs site found; no docs/whitepaper links |
| Stellar page | demo | https://tokeshare.co/stellar | — | Live page listing TRES and TFW_001 |
| French Tacos investor report Q1 2026 | pitch | https://tokeshare.co/French_Tacos_LT_SRL_Official_Investor_Report_Q1_2026_EN.pdf | — | Traction evidence for the Base asset |
| French Tacos marketplace page | demo | https://tokeshare.co/marketplace/other/french-tacos | — | Live EVM asset |
| BIM Exchange partnership post | blog | https://x.com/Tokeshare/status/2057511492958703970 | — | Post on X |
| Athéla café document | pitch | https://drive.google.com/file/d/1XdA2j4mVaOVUQ5q9vX_8O3J5MkFHza_i/view | — | File "athléa.pdf"; future asset pipeline, not product design |

## Gaps

- https://tokeshare.co/poc-stellar (the PoC named in the submission) returns 404. The Stellar app now appears at https://tokeshare.co/stellar.
- https://angelcoeurcaribe.com/ did not respond (timeout).
- "Crespa" and "Résidence Monopoly Villa 8 Village" documents are not linked (attached or "available on request").
- No docs site, whitepaper, audit or demo video found.
- No docs yet for tranche 2.2 or 3 (MoneyGram, alfredpay, SDP, CCTP, Soroswap, Aquarius). The repo plans cover only tranches 1 and 2.1.
