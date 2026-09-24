# Prism

Prism is a live "DeFi hub" (one app for crypto trading and saving tools: swaps, liquidity pools, futures, prediction markets, yield and a portfolio view). It runs today on MegaETH and Arbitrum, which are EVM chains (Ethereum-style blockchains). The team says it has 6,000+ users. With SCF #44 they add a Stellar "earn" module. An EVM user deposits USDC (a US-dollar stablecoin) in one click. The money moves to Stellar and buys "stablebonds" (government bonds turned into tokens, issued by Etherfuse on Stellar). It is for Prism's existing EVM users who want bond yield without learning Stellar.

## What SCF #44 pays them to build

Total award: $124.6K. Three tranches (payment stages), each with three deliverables.

- **Tranche 1 — MVP (first working version), $27,000**
  - EVM-to-Stellar USDC deposit in one signed transaction, via Circle CCTP V2 and NEAR Intents, with retries and status tracking — $14,200 (end of August 2026)
  - Stellar wallet connect (Stellar Wallets Kit, xBull, Freighter) and SEP-10 login sessions — $8,400 (end of August 2026)
  - Position engine for stablebond holdings: balance, accrued yield, multi-currency display with Reflector FX prices, history — $4,400 (early September 2026)
- **Tranche 2 — Testnet (Stellar's test network), $41,700**
  - Full deposit-and-earn flow that outside testers can complete; recorded demo — $16,200 (end of September 2026)
  - Exit path: redeem stablebonds, withdraw to Stellar or EVM wallet; big orders through the Soroswap Aggregator; optional idle cash parked in Blend — $16,300 (mid-October 2026)
  - Indexer (a service that saves ledger events to Prism's database) so history outlives Stellar RPC's short retention — $9,200 (mid-October 2026)
- **Tranche 3 — Mainnet (the real network), $55,900**
  - Earn module live on mainnet for all Prism users, public URL — $24,100 (mid-November 2026)
  - Custom Soroban strategy on the DeFindex vault framework, auto-compounding stablebonds, open-sourced — $18,400 (end of November 2026)
  - Hardening, monitoring and alerts, public docs, user rollout and user testing — $13,400 (end of November 2026)

## How it works

Source: the Technical Infrastructure Description v3.1 (`architecture.txt`).

- **Accounts.** Stellar wallet users connect with Stellar Wallets Kit and log in with SEP-10 (a sign-a-challenge login standard). EVM users never see Stellar: NEAR Chain Signatures create a Stellar account controlled by their EVM key. Prism pays that account's XLM reserves (sponsored reserves) and network fees (fee-bump transactions), so users hold no XLM.
- **Bridge.** The backend picks one of two routes. Circle CCTP V2 burns USDC on the source chain and mints it on Stellar (used for chains like Arbitrum). NEAR Intents (a network of "solvers" that fill cross-chain orders) covers chains CCTP does not, such as MegaETH. Withdrawals use the same routes in reverse.
- **Yield.** USDC buys Etherfuse stablebonds (first market: USTRY, US Treasury; later CETES, TESOURO, EUROB). Small orders use a path payment on SDEX (Stellar's built-in exchange). Big orders go through the Soroswap Aggregator, which splits across SDEX, Soroswap and Aquarius.
- **Strategy.** One custom Soroban (Stellar smart contract) strategy on DeFindex vaults. Version 1 holds only USTRY and reads no price oracle, to reduce risk. It is the only custom contract; the core flow needs none.
- **Idle cash.** Opt-in: unallocated USDC can be lent on Blend (a Stellar lending protocol).
- **Data.** Stellar RPC for reads and sends; an internal indexer stores events. Reflector (Stellar's oracle, a price-feed service) gives FX rates for display; the issuer's API gives bond redemption value.
- **Stack.** Prism's existing (private) backend and React app, Stellar JS SDK, Scaffold Stellar. Non-custodial: Prism cannot move user funds.

Public code: [HeylmStoned/prism-stellar-earn](https://github.com/HeylmStoned/prism-stellar-earn). Its README says only the wallet sandbox works on testnet today. The EVM deposit is "not wired" and the market cards are "preview only". The backend, position engine and production app are private.

## Documents

| Document | Type | Link | Local copy | Notes |
| --- | --- | --- | --- | --- |
| SCF #44 submission "One-Click Stellar Yield for EVM Users" | requirements | https://communityfund.stellar.org/submissions/recpWygA3kmg3NsZx | submission.md | Awarded $124.6K. Tranches, budgets, completion criteria. |
| Technical Infrastructure Description v3.1 (June 2026) | architecture | https://drive.google.com/file/d/1Bdc4mXJYlvKD5yvk4TXJ8pFqpCAQ7HsJ/view?usp=sharing | architecture.pdf, architecture.txt | 8-page PDF. Includes C4 diagrams (in PDF only), STRIDE threat model, deliverable-to-section map. |
| Repo architecture summary | architecture | https://github.com/HeylmStoned/prism-stellar-earn/blob/main/docs/architecture.md | repo-architecture.md | Says it matches doc "v1.5". Slightly different from v3.1: says stablebond buy/redeem runs through Etherfuse flows, not SDEX. |
| Repo README | docs | https://github.com/HeylmStoned/prism-stellar-earn | repo-readme.md | Lists what is live vs preview only, and what stays private. |
| SCF project page | earlier SCF submission | https://communityfund.stellar.org/project/rec6hDNJsh21YxDXb | — | Lists only the SCF #44 submission. No earlier rounds. |
| "Data room" Google Doc | pitch | https://docs.google.com/document/d/19EgMjeEF0QPqpxdkzUxCLqIHEUBj1tWSWIpF3kzJeY4/edit?usp=sharing | — | Public, but contains only one screenshot: 30-day web analytics for prismfi.cc (32,342 visitors, 174,543 page views, Apr 26 – May 24). No text. |
| Live POC (proof of concept) | demo | https://stellar.prismfi.cc/earn | — | Opens. Product preview plus testnet wallet sandbox. |
| Prism main app | docs site | https://prismfi.cc | — | Opens. The existing EVM app. No separate docs site found. |
| Arbitrum Mentorship selection post | blog | https://x.com/PrismFi_/status/2051725287402942589 | — | Traction evidence (X post). |
| Bad Bunnz post | blog | https://x.com/badbunnz_/status/2052031541900132763 | — | Team background (X post). |
| Distribution reach post | blog | https://x.com/PrismFi_/status/2019410846649032898 | — | Traction evidence (X post). |
| Growth post (from project page) | blog | https://x.com/PrismFi_/status/1991512945092669874 | — | Linked in the project description. |
| Analytics screenshot | pitch | https://gyazo.com/0837cf4d244c24887b537954d32b11d9 | — | Unreachable: 404, Gyazo "Under Maintenance". |

## Gaps

- The Gyazo analytics link did not open (404, maintenance page).
- The "data room" is only one analytics screenshot, not a real data room (no financials, no deck).
- No pitch deck, whitepaper, audit, demo video or public docs site found. Public docs are promised in Tranche 3.
- The public repo has no Soroban contract yet. The DeFindex strategy is promised for Tranche 3 and to be open-sourced after launch.
- The backend, position engine, indexer and production frontend are private, so most of the funded work cannot be checked from public code.
- The architecture diagrams (C4 figures) are images inside `architecture.pdf`; `architecture.txt` has only their captions.
- X posts return HTTP 200 but their content was not read (X needs a login to show them).
