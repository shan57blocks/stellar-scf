# Prism: One-Click Stellar Yield for EVM Users

Source: https://communityfund.stellar.org/submissions/recpWygA3kmg3NsZx (SCF #44, awarded $124.6K, category: End-User Application)

- Website: https://stellar.prismfi.cc/earn
- Architecture doc: https://drive.google.com/file/d/1Bdc4mXJYlvKD5yvk4TXJ8pFqpCAQ7HsJ/view?usp=sharing

## Links in the submission

- https://drive.google.com/file/d/1Bdc4mXJYlvKD5yvk4TXJ8pFqpCAQ7HsJ/view?usp=sharing
- https://github.com/HeylmStoned/prism-stellar-earn
- https://docs.google.com/document/d/19EgMjeEF0QPqpxdkzUxCLqIHEUBj1tWSWIpF3kzJeY4/edit?usp=sharing

## Submission text (as published)

```text
Products & Services
Prism is a live multichain DeFi hub — swaps, liquidity pools, perpetual futures, prediction markets, yield, and portfolio management — with 6,000+ organic users and $33M+ monthly volume since launching in March 2026. Selected into the Arbitrum Mentorship Program (13 teams from 900+ applicants), Prism is scaling beyond its early-adopter base into a broader consumer financial platform.
To get there, Prism needs what its current EVM stack alone cannot provide: institutional-grade settlement infrastructure, deep stablecoin liquidity, and regulated real-world asset integration. Stellar fills that gap. Its native SDEX, permissionless stablebond issuance by a regulated issuer (Etherfuse), and cross-chain USDC settlement via Circle CCTP V2 give Prism the financial infrastructure layer to offer sovereign bond yield that no EVM chain can replicate — multi-currency exposure (e.g., MXN, BRL, EUR bonds) whose deep liquidity sits on Stellar, not scattered across EVM AMMs.
The integration follows a three-step path:
1. Open cross-chain access
Circle CCTP V2
Cross-chain intents (e.g., NEAR Intents)
NEAR Chain Signatures
2. Integrate yield infrastructure
SDEX
Soroswap Aggregator
Blend
Etherfuse stablebonds
3. Make it feel native
DeFindex
Reflector
SEP-10
Stellar Wallets Kit
Stellar RPC
These twelve Stellar ecosystem components are packaged into two consumer-facing products.
A POC is already live, with the full product interface deployed and a functional Stellar wallet integration on testnet:
https://stellar.prismfi.cc/earn
Product 1 — One-click Stablebond Access
One-click USDC deposit from any EVM wallet into Stellar-native sovereign bond yield.
Flow:
Circle CCTP V2 for USDC settlement
Cross-chain intents for execution
Stablebonds acquired permissionlessly on the SDEX
Larger orders routed through Soroswap Aggregator (SDEX, Soroswap, Aquarius)
Idle USDC deployed on Blend until execution
Stellar acts as the settlement, trading, and asset layer.
Impact
Brings an existing EVM user base onto Stellar rails weeks after CCTP's launch
Unlocks multi-currency sovereign exposure (MXN, BRL, EUR bonds) whose liquidity exists primarily on Stellar
Product 2 — Automated Stablebond Vault
A custom Soroban strategy built on DeFindex's vault framework, aggregating stablebond exposure with automated compounding and open-sourced post-launch.
Stellar acts as the DeFi strategy layer.
Impact
Routes TVL into the SCF-funded composability stack
Leverages DeFindex vaults and Blend liquidity infrastructure
Adds a reusable open-source yield strategy to the ecosystem
Revenue & Sustainability
Prism monetizes through yield-generated fees rather than principal-based fees:
Performance fee on the DeFindex strategy
Blend Fee Vault revenue on idle yield
Fees are intentionally kept low to maintain competitive net returns.
The entire model is implemented through native Stellar frameworks and on-chain mechanisms, requiring no custom billing infrastructure. This allows the module to remain self-sustaining post-grant with near-zero marginal cost while continuously routing new TVL, trading volume, and active users into the Stellar ecosystem.
Requested Budget
$124.6K
Traction Evidence
Prism launched in March 2026 and onboarded 6,000+ users in its first three months with zero paid acquisition. During that period:
Monthly volume grew from $6.95M (March) to $33.3M (May)
TVL peaked at $4M
Monthly revenue reached $10k–15k
Protocol fees grew from $12,984 (March) to $43,523 (May)
Prism was selected into the Arbitrum Mentorship Program, with only 13 teams chosen from more than 900 applicants:
https://x.com/PrismFi_/status/2051725287402942589
The founding team also co-founded Bad Bunnz, one of the largest NFT community initiatives of 2025:
https://x.com/badbunnz_/status/2052031541900132763
Beyond product traction, Prism benefits from significant distribution capabilities. Through the founders' communities, the project reaches approximately 500,000 users per month across social channels:
https://x.com/PrismFi_/status/2019410846649032898
Most funded integrations face a cold-start problem after launch. Prism does not. The Stellar integration will launch to an existing base of 6,000+ users and benefit from immediate amplification through Prism's established distribution network. The award funds the build; the audience already exists.
Analytics:
https://gyazo.com/0837cf4d244c24887b537954d32b11d9
Data room:
https://docs.google.com/document/d/19EgMjeEF0QPqpxdkzUxCLqIHEUBj1tWSWIpF3kzJeY4/edit?usp=sharing
Tranche 1 (Deliverable Roadmap) - MVP
Deliverable 1 — Production-grade EVM-to-Stellar deposit flow
Brief description
Implement the production deposit flow behind the deployed interface: one-transaction USDC deposit from an EVM wallet to a Stellar account via cross-chain USDC routing, including canonical burn-and-mint routing (e.g. Circle CCTP V2) and cross-chain intents (e.g. NEAR Intents), with backend route selection, error handling, retry logic, and transaction status tracking.
How to measure completion
A user can deposit USDC from an EVM wallet and receive it on a Stellar account from a single signed transaction, demonstrated in staging through a recorded flow over one routing path.
Estimated date of completion
End of August 2026
Budget
$14,200
Deliverable 2 — Stellar wallet connectivity and SEP-10 sessions
Brief description
Add native Stellar wallet support in the Prism interface through standard ecosystem wallet libraries such as Stellar Wallets Kit, with priority support for major wallets such as xBull and Freighter. Implement SEP-10 web authentication to issue persistent sessions tied to Stellar accounts, enabling portfolio access across user sessions.
How to measure completion
Users can connect a supported Stellar wallet, authenticate via SEP-10, view balances, and sign transactions directly from the Prism UI in staging.
Estimated date of completion
End of August 2026
Budget
$8,400
Deliverable 3 — RWA yield position engine
Brief description
Implement position accounting for tokenized sovereign stablebond holdings, such as Etherfuse assets, within Prism's existing portfolio engine. This includes balances, accrued yield through issuer redemption values, multi-currency display using FX feeds such as Reflector, and transaction history.
How to measure completion
A staging user can view a Stellar yield position with accurate balance, accrued value, and transaction history in their Prism portfolio.
Estimated date of completion
Early September 2026
Budget
$4,400
Tranche 2 (Deliverable Roadmap) - Testnet
Deliverable 1 — End-to-end deposit and earn flow
Brief description
Deploy the complete user journey in a test environment, combining Stellar testnet and staging infrastructure for the cross-chain layer. The flow includes EVM USDC deposit, Stellar settlement, permissionless stablebond acquisition on the SDEX (e.g. Etherfuse), and portfolio tracking within Prism.
How to measure completion
External testers can successfully complete the full deposit and earn flow in the test environment. A complete demo is recorded and shared with reviewers.
Estimated date of completion
End of September 2026
Budget
$16,200
Deliverable 2 — Redemption, aggregator routing, and idle yield
Brief description
Implement the full exit path, including stablebond redemption on the SDEX and withdrawals to either a Stellar wallet or an EVM wallet through the routing layer. Integrate large-order routing via the Soroswap Aggregator (across venues such as SDEX, Soroswap, and Aquarius) and an optional idle-yield mechanism supplying pending-allocation USDC to a lending market such as Blend.
How to measure completion
An external tester can execute the complete lifecycle (deposit, earn, redeem, withdraw) within the test environment. Aggregator routing and idle-yield functionality are demonstrated where liquidity is available.
Estimated date of completion
Mid-October 2026
Budget
$16,300
Deliverable 3 — Position history indexer and portfolio integration
Brief description
Build an internal indexing service that captures relevant Stellar ledger events and stores them within Prism's infrastructure, enabling durable position history beyond RPC retention limits. Integrate indexed data into Prism's existing multichain portfolio dashboard.
How to measure completion
Positions, balances, and transaction history display consistently across sessions and accurately reflect on-ledger activity within the test environment.
Estimated date of completion
Mid-October 2026
Budget
$9,200
Tranche 3 (Deliverable Roadmap) - Mainnet
Deliverable 1 — Mainnet launch of the Stellar earn module
Brief description
Deploy the Stellar earn module to production within the live Prism application, including deposit, yield generation, portfolio tracking, and redemption functionality. The module will be publicly accessible and available to all Prism users.
How to measure completion
The module is publicly accessible on mainnet and processes real user deposits into stablebond-based yield positions. A public URL is shared with reviewers.
Estimated date of completion
Mid-November 2026
Budget
$24,100
Deliverable 2 — Custom DeFindex vault strategy (Soroban)
Brief description
Develop and deploy a custom Soroban strategy built on the DeFindex vault framework, aggregating stablebond exposure with automated compounding. The initial version will be single-asset and oracle-free by design to minimize complexity and risk. The strategy will be open-sourced under a permissive license after launch.
How to measure completion
The strategy is deployed on mainnet, accepts deposits through the Prism interface, and the source code is publicly available.
Estimated date of completion
End of November 2026
Budget
$18,400
Deliverable 3 — Launch hardening, monitoring, and user rollout
Brief description
Complete production hardening of the integration, including live withdrawals back to EVM wallets, monitoring and alerting for settlement latency, transfer failures, account sponsorship issues, and idle-yield pool utilization. Publish public documentation, onboard Prism's existing user base, and conduct professional user testing in line with the SCF process.
How to measure completion
Reviewers can verify a complete production round trip (deposit, earn, redeem, withdraw). Monitoring systems are operational, documentation is publicly available, and user testing has been completed.
Estimated date of completion
End of November 2026
Budget
$13,400
Team
Beacon
— Co-Founder
Former lawyer specialized in family office and asset management. Active in the crypto industry since 2017, with deep expertise across multi-chain ecosystems, emerging market trends, and digital asset strategies. He has built a strong presence within the crypto community through educational and research-driven content, reaching more than 25,000 followers on X. He also co-founded BadBunnz, one of the most active NFT-focused communities launched in 2025.
Pasheur
— Co-Founder
Former Head of Marketing within LVMH, where he spent more than six years leading marketing initiatives for global brands. Active trader and DeFi specialist with extensive experience across on-chain ecosystems, user acquisition, and product growth. He has built one of the largest French-speaking DeFi communities, reaching more than 20,000 followers on X through educational content focused on DeFi, market opportunities, and emerging protocols. He also co-founded BadBunnz, a leading crypto-native community initiative.
Alex
— Chief Technology Officer
Experienced Web3 engineer with expertise in smart contracts, protocol architecture, and product development. Previously worked with VeChain, Elysium, and initiatives linked to MegaETH. Led multiple engineering teams and delivered blockchain products from design to production.
mXdr — Chief Operating Officer
Leads operations, partnerships, product coordination, marketing execution, and community growth. Has worked closely with the founding team for more than 18 months and oversees day-to-day execution across Prism's multi-chain expansion.
Beacon
```
