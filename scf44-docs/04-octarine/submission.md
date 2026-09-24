# Octarine: RFQ for RWA instant redemptions

Source: https://communityfund.stellar.org/submissions/recbRpXosPJ3lhhgr (SCF #44, awarded $129.7K, category: Financial Protocols)

- Website: https://octarine.finance/
- Architecture doc: https://github.com/mystic-finance/Stellar-RFQ/blob/main/docs/TECHNICAL_ARCHITECTURE.md

## Links in the submission

- https://github.com/mystic-finance/Stellar-RFQ/blob/main/docs/TECHNICAL_ARCHITECTURE.md
- https://github.com/mystic-finance/Stellar-RFQ/tree/main
- https://stellar.expert/explorer/testnet/contract/CAPVBMQBVQVDFDWFGH4M3EJH7CYM7MWIYE5TOYTYASOU26L2Q4T2YJZW
- https://github.com/mystic-finance/Stellar-RFQ
- https://drive.google.com/drive/folders/1v6b4PI1T2PKhICDVZjv9Ifjd77_IqHw6?usp=sharing

## Submission text (as published)

```text
Products & Services
Problem
Real World Assets (RWAs), such as tokenized real estate, bonds, and commodities, represent a multi-trillion-dollar market, but they remain almost unusable in DeFi today. The reason is simple: there is no instant liquidity for these assets.
In practice, an RWA holder who wants to sell must wait for the issuer’s redemption cycle, which can take days or even weeks. This lack of immediate liquidity creates three direct consequences.
No DeFi collateral: Lending protocols cannot accept RWAs as collateral because DeFi liquidations need to happen instantly. If the collateral cannot be sold within seconds, the system is exposed to risk.
No leverage: Users cannot build leveraged strategies on RWAs because they cannot unwind their positions instantly.
No LPs: Liquidity providers avoid RWAs because their capital can remain locked during the redemption period, with no fast exit option.
As a result, RWAs remain isolated from the rest of the DeFi ecosystem, with no functional secondary market.
The settlement contract is already functional and deployed on
Stellar Testnet.
 The source code is available on
GitHub.
 A live preview of the Stellar interface is
available.
Octarine Solution
Octarine creates an instant liquidity market for RWAs by putting multiple liquidity sources in competition through an auction system, with atomic settlement on Stellar.
Real-time competitive auctions: When an RWA holder wants to sell, Octarine launches an auction between professional LPs and curated liquidity facilities. The best price wins. The seller receives stablecoins within seconds.
Atomic and non-custodial settlement: Each trade settles in a single transaction on Stellar: the RWA and the stablecoins change hands simultaneously, or nothing happens. Octarine never holds keys or funds at any point. Every operation is signed by the user’s wallet.
Curated liquidity facilities: Professional managers create vaults that generate yield on deposits and automatically bid on RWAs. When a facility wins, it pays the seller in stablecoins, receives the RWA, and redeems it with the issuer. The redemption profit is redistributed to depositors.
Automated liquidations: Octarine continuously monitors lending positions across connected DeFi protocols. When a position becomes insolvent, a liquidation auction is triggered automatically. This makes RWAs usable as DeFi collateral, a use case that has not been possible until now.
Modular integration: Each Stellar DeFi protocol, including lending markets, AMMs, and yield vaults, is connected through a dedicated adapter. Adding a new protocol does not require modifying the existing system, enabling progressive integration with the ecosystem, including Blend, Aquarius, and DeFindex.
Impact on the Stellar Ecosystem
Octarine generates concrete activity on the Stellar network.
More transactions: Each RWA swap produces on-chain transactions on Soroban.
More TVL: Liquidity facilities lock stablecoins into Soroban contracts, increasing TVL across the Stellar DeFi ecosystem.
New use case: By making RWAs instantly liquidatable, Octarine opens DeFi to an entire asset class that has so far been excluded from it. Lending protocols on Stellar can now accept RWAs as collateral because liquidations can be executed through Octarine’s auction mechanism.
Composability: The modular adapter-based architecture integrates with existing Stellar ecosystem protocols, strengthening network composability
Requested Budget
$129.7K
Traction Evidence
$700,000 in ACRED
 instant redemptions processed, the highest volume handled by any DeFi protocol to date. The transaction reproduced exactly the OTC use case that the protocol is designed to automate.
7 institutional LP partnerships secured, including
GSR
,
Auros
 and
Maven11
 (
Private Google Drive
) The Drive is private and access to it can be shared upon request. If you are given access to it, don’t disclose its information or the prints with anyone - the identity of our LPs and their relationship with us is private and must remain that way, it is not meant for disclosing.
DigiFT integration live, with RFQ bids routed through their MAS-regulated broker-dealer license in Singapore, enabling the first fully regulated secondary market for RWAs in DeFi.
Stellar-specific traction:
Franklin Templeton’s BENJI is already live on Stellar. The team will provide instant secondary market liquidity for BENJI through the DigiFT integration and LP network.
Active discussions are ongoing with all major Stellar RWA issuers. Several are already confirmed as serviceable by the existing LP network.
ACRED (Securitize) & ACRDX (Centrifuge) secondary markets. LP secured, currently in DD with Securitize. This will become the first fully functional ACRED secondary market, directly supporting Stellar's RWA strategy.
Broader ecosystem traction:
DigiFT assets: GSR providing instant liquidity for bEQTY, DYNA, UMINT, CMBMINT, plus third-party assets including Libeara's ULTRA. Live imminently.
Plume / Nest integration: Auros providing instant liquidity for nOPAL, nALPHA, nBASIS, nETHERFI, nCRDYX, FXCF and more. Live shortly after DigiFT.
Pareto: Maven11 market-making AA_FalconxUSDC auctions, with additional LP backup in place. Frontrunners in pipeline include Prime (Figure) and AA_RockawayxUSDC.These partnerships validate the protocol's market fit and LP model, which the team is now bringing to Stellar.
Tranche 1 (Deliverable Roadmap) - MVP
Deliverable 1 — RFQ Settlement Smart Contracts
Description:
 Enable auctions and atomic trade settlements on Stellar, bringing RWA instant liquidity to the network. This implementation services classic assets, features SEP-53 signature verification, SAC allowances and atomic transfer mechanisms. Supports RFQ orders, limit orders, fill-or-kill variants, delegated signers and pair-level cancellation.
Completion:
 RFQ auctions can be conducted and settled on Stellar Testnet via direct contract calls. All order types functional with protocol fee collection.
Estimated date of completion:
 Jul 15, 2026
Budget:
 $14,600
Deliverable 2 — RFQ Router
Description:
 A contract that aggregates pricing from multiple sources, signed LP bids and on-chain facility quotes, selects the best price and settles the winning route atomically. Enforces a taker-specified minimum output, reverting the whole transaction if the price isn't met.
Completion:
 Router deployed on Testnet, connected to the Settlement Contract. Atomic fill demonstrated with best-price selection and revert on insufficient output.
Estimated date of completion:
 Jul 25, 2026
Budget:
 $12,100
Deliverable 3 — Auction Backend, API & Frontend MVP
Description:
 Adjust offchain quoting and auctions to fit Stellar. Backend handles RFQ broadcast, bid collection and ranking, and assembles unsigned transactions for wallet signing. REST API for LPs to bid on auctions programmatically. React frontend with Stellar Wallets Kit integration so users can create swaps, view live auctions and bids, and sign transactions directly from their wallet.
Completion:
 Possible to bid via API and via UI. UI shows both bids and live auctions and enables users to create swaps themselves. End-to-end flow from UI to on-chain settlement on Testnet.
Estimated date of completion:
 Aug 4, 2026
Budget:
 $13,000
Tranche 2 (Deliverable Roadmap) - Testnet
Deliverable 1 — Liquidity Facility & Aggregator Contracts
Description:
 Expand the protocol to support bids from curated liquidity facilities, vaults that bidders can create to make their bidding strategy modular in DeFi. These facilities receive deposits, deploy them into whitelisted venues (e.g. lending markets) to earn yield, and bid on RFQs with that TVL. When a facility wins, it pulls liquidity from venues, pays the taker in stablecoins, takes the RWA, and redeems it with the issuer. A Facility Aggregator contract aggregates bids across all facilities and routes the winning fill through the RFQ Router.
Completion:
 A curated liquidity facility on Stellar Testnet can receive deposits, bid on RFQs through the Aggregator and Router, settle atomically, and book the acquired RWA for redemption.
Estimated date of completion:
 Aug 24, 2026
Budget:
 $19,800
Deliverable 2 — Venue Adapters & RWA Pricing
Description:
 Two adapters integrating with key protocols on Stellar (Blend v2 lending market and DeFindex yield protocol), so facilities are modular with Stellar DeFi from the start. Adding a new protocol means deploying one adapter, never touching the facility or router code. NAV-based pricing pipeline so facilities automatically quote based on the issuer's published asset value, with staleness checks to stop quoting if the NAV is outdated.
Completion:
 Facilities can allocate stablecoins to Blend v2 and DeFindex, earn yield, and pull liquidity on demand during an RFQ fill. Pricing updates automatically based on issuer NAV.
Estimated date of completion:
 Sep 3, 2026
Budget:
 $13,900
Deliverable 3 — Curator Console & LP SDK
Description:
 Curator-facing UI for facility creation and management, set pricing parameters, whitelist venues and adapters, monitor NAV and share balances, pause/resume. TypeScript SDK enabling LPs to connect to the auction API, submit signed bids and monitor fills, with a documented example bot implementation.
Completion:
 A facility can be created and its parameters set via the curator UI. LP SDK operational with example bot. Clear documentation for third-party LP integration.
Estimated date of completion:
 Sep 13, 2026
Budget:
 $10,400
Tranche 3 (Deliverable Roadmap) - Mainnet
Deliverable 1 — Keeper Bots & Infrastructure
Description:
 Production keeper bots for liquidation detection on connected lending markets (triggering RFQ auctions automatically), storage TTL management, and event indexing to MongoDB. Production-ready infrastructure for blockchain indexing, monitoring and alerting. Docker-packaged and deployed with automated health checks.
Completion:
 Keepers operational on Testnet, liquidation detection, TTL extension and event indexing verified. Monitoring and alerting functional.
Estimated date of completion:
 Sep 28, 2026
Budget:
 $14,200
Deliverable 2 — Production Frontend
Description:
 Full production frontend: swap/redeem with live auction status, facility deposit/withdraw with NAV tracking, LP bid flows, curator dashboard with analytics, and transaction history.
Completion:
 All user flows (taker, LP, depositor, curator) functional end-to-end on Testnet.
Estimated date of completion:
 Oct 8, 2026
Budget:
 $17,300
Deliverable 3 — Mainnet Deployment
Description:
 Deploy all smart contracts, backend, keepers and frontend to Stellar Mainnet. Initialize with production parameters. Full app working end-to-end on Mainnet — RFQ requests can be filled by LPs and by integrated liquidity facilities, users can deposit/withdraw into facilities, and curators can create and manage their own facilities.
Completion:
 Full app working end-to-end on Stellar Mainnet. Smart contracts deployed with published addresses. First settlement transaction verified on-chain.
Estimated date of completion:
 Oct 15, 2026
Budget:
 $14,100
Team
João Moreira, CEO -
Linkedin
Previously sales at Microsoft, did an AI startup and then went into PE as CEO of a SaaS business. Got into crypto in late 2021, and spend 2022-2023 building NFT infra until he and John met in early 2024 to focus on RWAs
John Agbanusi, CTO -
Github -
Linkedin
Previously Smart Contract Engineer at GooseFX and PlayTreks, John was building his own portfolio tracking startup in early 2024 when he and João met
Tiago Vasconcelos, CMO -
X
Previously marketing at Wormhole, Seda and marketing advisor at Outlier Ventures, Tiago brings a breadth of comms experience that is invaluable for the Mystic brand.
Roberto Machado, Advisor -
Linkedin
Previously co-founder at UTrust (exited) and now founder of web3 venture studio Subvisual, Roberto helps us with all things strategy, internal management and operations
Progress Aienobe, Fullstack Engineer -
Github
 -
Linkedin
Bachelors in Mathematics and 4 years of experience in fullstack blockchain engineering. Did an NFT marketplace, an NFT OTC desk, a DEX and a lending market all by himself before
Samuel Adegbite, Marketing Manager -
Linkedin
Previously Community and Marketing Manager at TokenSuite and CoinTerminal, Sammy handles in all things marketing and community
Joao Moreira
```
