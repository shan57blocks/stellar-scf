# Etesia

Etesia Research brings "trend-following" investing to Stellar. Trend following means a computer rule buys assets that are rising and steps out (or goes short) when they fall. Etesia already runs such a strategy in a vault on another blockchain (Hyperliquid). On Stellar it will build two vaults (shared pots of money run by a strategy) and a free "Portfolio Builder" tool that helps people spread money across assets so each one adds the same amount of risk. It is for crypto investors who want returns that do not just move with Bitcoin. Users keep control of their funds (non-custodial).

## What SCF #44 pays them to build

Total award: $113,000.

- **Tranche 1 — MVP** ($41,800)
  - D1: trend-signal engine and risk-balancing ("risk parity") allocation engine in Python, plus backtesting — $15,800, due 29.07.2026
  - D2: vault smart contracts on Soroban following SEP-56 (Stellar's vault standard), with on-chain safety limits — $16,400, due 12.08.2026
  - D3: pricing from Soroswap average prices (TWAP), with the Reflector oracle as a safety switch — $9,600, due 26.08.2026
- **Tranche 2 — Testnet** ($36,100)
  - D4: off-chain "orchestrator" that rebalances the vault via Soroswap swaps and parks idle stablecoins in Blend v2 for yield — $12,700, due 09.09.2026
  - D5: Portfolio Builder web app with Stellar Wallets Kit (Freighter, xBull) and an event indexer — $13,000, due 23.09.2026
  - D6: stress tests, monitoring, runbooks, mainnet readiness — $10,400, due 07.10.2026
- **Tranche 3 — Mainnet** ($35,100)
  - D7: mainnet launch of the long-only vault and Portfolio Builder; vaults expose the DeFindex strategy interface — $11,900, due 21.10.2026
  - D8: data, volatility and liquidity models for the long-short vault — $12,000, due 04.11.2026
  - D9: launch of the long-short vault, trading on a Stellar perpetual-futures venue (win-trader first choice) or "synthetic shorts" built from Blend v2 borrowing plus Soroswap swaps — $11,200, due 15.11.2026

(Tranche totals are the sums of the deliverable budgets in the submission.)

## How it works

The rule is "thinking off-chain, money on-chain".

- **Python engines (off-chain):** compute trend signals and risk-parity weights. This math is too heavy for smart contracts.
- **Orchestrator (Node.js/TypeScript):** compares targets with what the vault holds, publishes the new targets on-chain, then sends a rebalance plan.
- **Vault contract (Soroban, Rust):** holds user money. Follows SEP-56, with shares as SEP-41 tokens (Stellar's token standard). It checks every plan against fixed limits (allowed assets, maximum price slippage, size caps, waiting times, pause switch) and rejects anything outside them. So a hacked backend cannot take funds; at worst a rebalance is missed. Withdrawals do not need Etesia's servers.
- **Pricing:** Soroswap time-weighted average prices set the minimum each swap must return; the Reflector oracle stops trading if prices are stale or disagree.
- **Integrations:** Soroswap for swaps (Phoenix as backup), Blend v2 for stablecoin yield and for synthetic shorts, DeFindex so other apps can plug the vaults in (`invest`, `unwind`, `harvest`), Stellar Wallets Kit for wallet connection.
- **Portfolio Builder (Next.js):** lets users pick assets and allocate. An indexer (PostgreSQL) serves performance data.

Public code (source-visible, not open source): `stellar_vault` (Soroban contracts, with docs for deliverables D2 and D3) and `etesia-portfolio-builder` (Next.js front end). Both use a "Proprietary Source Inspection License": you may read the code, but copying or running it needs written permission.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recuUVlYWqrAk5t9Z | submission.md | Only SCF submission on the project page |
| Technical Architecture (PDF, June 2026) | architecture | https://drive.google.com/file/d/1UFbmHJhViw-Jee8Zq4O7ots8-enPfmWF/view?usp=sharing | architecture.pdf, architecture.txt | 16 pages: integrations, contract design, threat model, tranche mapping |
| SCF project page | earlier SCF submission | https://communityfund.stellar.org/project/rec2NiSq2oNtNDrgJ | — | Lists only the SCF #44 submission; no earlier rounds |
| stellar_vault repo | spec | https://github.com/Etesia-Research/stellar_vault | — | Soroban vault and pricing contracts |
| Vault interface contract (D2) | spec | https://github.com/Etesia-Research/stellar_vault/blob/master/docs/interfaces.md | — | Not copied: license forbids copying |
| Pricing provider (D3) | spec | https://github.com/Etesia-Research/stellar_vault/blob/master/docs/pricing.md | — | Not copied: license forbids copying |
| Operations and recovery | docs | https://github.com/Etesia-Research/stellar_vault/blob/master/docs/operations.md | — | Not copied: license forbids copying |
| D2 acceptance evidence | docs | https://github.com/Etesia-Research/stellar_vault/blob/master/docs/acceptance.md | — | Local test results, 18.09.2026 |
| D3 acceptance evidence | docs | https://github.com/Etesia-Research/stellar_vault/blob/master/docs/d3-acceptance.md | — | Says full D3 acceptance is still blocked |
| Local RPC guide | docs | https://github.com/Etesia-Research/stellar_vault/blob/master/docs/local-rpc.md | — | How to reproduce the local test deployment |
| etesia-portfolio-builder repo | docs | https://github.com/Etesia-Research/etesia-portfolio-builder | — | README covers wallets, data, allocation; execution still simulated |
| Etesia docs site | docs site | https://docs.etesiar.com/ | — | About the Hyperliquid vault; no Stellar pages |
| Trend Following – Systematic Macro Trading | blog | https://www.etesiar.com/blog/trend-following-systematic-macro-trading | research-article.md | "Research Article" cited in the submission |
| Website | docs site | https://www.etesiar.com/ | — | |
| Stellar proof of concept (Portfolio Builder) | demo | https://portfolio.etesiar.com/ | — | |
| "Etesia Research Full Demo" (vision video) | demo | https://www.youtube.com/watch?v=e9HTVoKcmGQ | — | |
| "Etesia Research Portfolio Atelier" (proof-of-concept demo) | demo | https://www.youtube.com/watch?v=bIOcZfIbrZk | — | |
| Letters of intent (Drive folder) | pitch | https://drive.google.com/drive/folders/1nTWFH6vzMwose8P1IvOJJkPZPCJxqAGF?usp=sharing | — | 10 signed letters of intent (PDFs) as traction evidence; not copied |
| Hyperliquid vault dashboard | demo | https://app.etesiar.com/ | — | Live track record on Hyperliquid |
| Lagoon vault page | demo | https://app.lagoon.finance/vault/999/0xb718bdaa857d5ab82c09c7f0c75bfba2f831090a#details | — | Third-party view of the Hyperliquid vault |

## Gaps

- No Stellar-specific docs site. docs.etesiar.com covers only the Hyperliquid vault.
- The code repos are source-visible under a proprietary license, not open source. Their docs are linked, not copied, because the license forbids copying.
- No public repo for the Python engines, the orchestrator or the indexer.
- The repo's D2 acceptance doc links to a plan (`doc/d2-vault-implementation-plan.md`) that sits outside the repo and is not public.
- No audit report yet. The architecture doc plans a third-party audit before mainnet.
- The long-short vault's venue is still open: win-trader, Zenex or Noether, or synthetic shorts.
