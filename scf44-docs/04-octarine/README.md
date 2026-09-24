# Octarine

Octarine lets people who hold real-world assets (RWAs: tokens that stand for things like funds, bonds or T-bills) sell them for stablecoins right away. Normally they must wait days or weeks for the issuer to buy the token back (a "redemption"). Octarine runs a quick auction instead. Professional trading firms (LPs, "liquidity providers") and curated vaults bid, and the best price wins. The trade settles in one Stellar transaction. It is for RWA holders, for trading firms that want to buy RWAs at a discount, and for lending apps that need to sell RWA collateral fast. It is built by the Mystic Finance team. SCF #44 awarded $129.7K.

## What SCF #44 pays them to build

Tranche 1 — MVP
- RFQ settlement contracts ($14,600, due Jul 15, 2026). RFQ means "request for quote": the seller asks, bidders answer with prices.
- RFQ router contract that picks the best price and reverts if the seller's minimum is not met ($12,100, Jul 25, 2026).
- Auction backend, REST API for LP bids, and React frontend with Stellar Wallets Kit ($13,000, Aug 4, 2026).

Tranche 2 — Testnet
- Liquidity facility (vault) and facility aggregator contracts ($19,800, Aug 24, 2026).
- Adapters for Blend v2 and DeFindex, plus pricing from the issuer's published asset value (NAV) ($13,900, Sep 3, 2026).
- Curator console and TypeScript LP SDK with an example bot ($10,400, Sep 13, 2026).

Tranche 3 — Mainnet
- Keeper bots (automated background programs) for liquidations, storage upkeep and event indexing ($14,200, Sep 28, 2026).
- Production frontend for all user types ($17,300, Oct 8, 2026).
- Mainnet deployment of everything ($14,100, Oct 15, 2026).

The team's Tranche 1 completion report (in the repo) says all three Tranche 1 items are done on testnet.

## How it works

- **Off-chain part (backend).** A NestJS server with a MongoDB database. It publishes a sell request, collects bids, ranks them, checks signatures and builds the unsigned transaction. It holds no keys or funds. Users sign in their own wallet.
- **Settlement contract (Soroban, Stellar's smart contract platform).** Swaps the RWA and the stablecoin in one step, or nothing happens. It takes the protocol fee in the same step. Both sides sign off-chain with SEP-53 (Stellar's message-signing standard). Tokens move through allowances on Stellar Asset Contracts (SAC), so the contract does not hold funds, except for "Dutch" listings where the price falls over time.
- **Pricing.** Bids are a rate in basis points per day, charged over the time until the asset can be redeemed. Prices come from an oracle adapter that reads a SEP-40 price feed (Reflector).
- **RFQ router contract.** Looks at signed LP bids and on-chain vault quotes, and settles the chosen route atomically with a minimum-output check.
- **Liquidity facilities and aggregator (Tranche 2).** Vaults run by a "curator". They park deposits in lending markets through one adapter per venue (Blend v2, DeFindex), and pull the money out when they win an auction. The aggregator finds the best vault bid.
- **Keepers (Tranche 3).** Bots that spot unhealthy loans on connected lending markets, start liquidation auctions, and extend Soroban storage lifetimes (TTL).

Testnet contracts from the Tranche 1 report: settlement `CDB75DJB…`, router `CAVJVJ7Q…`, price adapter `CAGR33LO…`. The architecture doc and submission name an older settlement address `CAPVBMQB…`.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recbRpXosPJ3lhhgr | submission.md | Tranches, budgets, completion criteria |
| SCF project page | earlier SCF submission | https://communityfund.stellar.org/project/recY1tzi4PgrhbVWL | — | Lists only this one submission (SCF #44); no earlier rounds |
| Technical Architecture | architecture | https://github.com/mystic-finance/Stellar-RFQ/blob/main/docs/TECHNICAL_ARCHITECTURE.md | architecture.md | Contracts, flows, storage, events, pricing, threat model |
| Stellar-RFQ README | spec | https://github.com/mystic-finance/Stellar-RFQ/blob/main/README.md | repo-readme.md | Pricing math, order types, oracle adapter, router, as built |
| Tranche 1 completion report | spec | https://github.com/mystic-finance/Stellar-RFQ/blob/main/docs/MILESTONE_1.md | milestone-1.md | What was delivered, testnet addresses, how to test |
| Octarine docs site | docs site | https://docs.octarine.finance | docs-site.md | Intro, how it works, addresses. Covers the existing EVM product; no Stellar pages |
| Contracts repo | code | https://github.com/mystic-finance/Stellar-RFQ | — | Soroban contracts, no license file |
| Tranche 1 demo video | demo | https://www.loom.com/share/e4664b4ee06144ac9e464ebf2fba1b9d | — | "Stellar Milestone 1 Video - 1 September 2026" |
| Backend API docs | docs site | https://curator-api.mysticfinance.xyz/docs/#/Octarine | — | Swagger API reference |
| Testnet app | demo | https://stellar-setup.octarine-ui.pages.dev | — | Live testnet UI |
| Website | website | https://octarine.finance/ | — | |
| Settlement contract (older) | demo | https://stellar.expert/explorer/testnet/contract/CAPVBMQBVQVDFDWFGH4M3EJH7CYM7MWIYE5TOYTYASOU26L2Q4T2YJZW | — | Address from the submission |
| Settlement contract (Tranche 1) | demo | https://stellar.expert/explorer/testnet/contract/CDB75DJB7KK6V2CJPGT44CJRZYPP7BPXFHZTOPIYSGO2KGQC576UJYQM | — | Address from the completion report |
| LP partnerships evidence | pitch | https://drive.google.com/drive/folders/1v6b4PI1T2PKhICDVZjv9Ifjd77_IqHw6 | — | Private, asks for Google sign-in |
| Backend repo | code | https://github.com/mystic-finance/backend/tree/alt-staging | — | Private (404) |
| Frontend repo | code | https://github.com/mystic-finance/Octarine-UI/tree/stellar-setup | — | Private (404) |

## Gaps

- The Google Drive folder with LP partnership evidence is private. The submission says access is only given on request.
- Backend and frontend repos are private. The team offers access if you send a GitHub username.
- The docs site describes the existing Ethereum product only. There are no public Stellar user docs yet.
- No pitch deck, audit or whitepaper is linked.
- No earlier SCF submissions exist for this project.
