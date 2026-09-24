# Tokeshare: LATAM Real-World Investing on Stellar

Source: https://communityfund.stellar.org/submissions/recFXz7DtcwZ9JzFJ (SCF #44, awarded $133.0K, category: End-User Application)

- Website: https://tokeshare.co/
- Architecture doc: https://drive.google.com/file/d/1nU1CmwaCQZplYEVGsnmKAi6jh5NilPSb/view?usp=drivesdk

## Links in the submission

- https://drive.google.com/file/d/1nU1CmwaCQZplYEVGsnmKAi6jh5NilPSb/view?usp=drivesdk
- https://github.com/bim-finance-org/tokeshare
- https://tokeshare.co/French_Tacos_LT_SRL_Official_Investor_Report_Q1_2026_EN.pdf
- https://drive.google.com/file/d/1XdA2j4mVaOVUQ5q9vX_8O3J5MkFHza_i/view

## Submission text (as published)

```text
Products & Services
Tokeshare
Tokeshare is a multi-chain tokenization platform for physical, revenue-generating assets — rental properties, operating businesses (e.g., restaurants), vehicles, and commodities (e.g., gold).
Unlike financial RWA platforms that tokenize bonds or treasury products, Tokeshare tokenizes assets in the real economy: the yield comes from actual tenants paying rent, customers buying food, or vehicles being rented — not from financial instruments.
Current traction
Live on Polygon, Ethereum, and Base
$43,945+ in tokenized assets
722 shares sold to real investors
Live USDC revenue distribution
Soroban sale contract already deployed and live at:
tokeshare.co/poc-stellar
Stellar Wallets Kit already integrated
This submission integrates Tokeshare onto Stellar through 8 integrations from the Integration List, organized into 3 chapters.
CHAPTER 1 — WALLET & ONBOARDING
Stellar Wallets Kit
Wallet connection (Lobstr, Freighter, xBull)
Multi-asset purchase flow
Automatic trustline management
Portfolio view
Already integrated in the live PoC.
Privy
Embedded wallet via email/social login for non-crypto LATAM investors.
No seed phrase, no browser extension — just an email to start investing.
CHAPTER 2 — FIAT ON/OFF-RAMP LATAM
MoneyGram
Cash-in/cash-out at 400,000+ physical locations.
LATAM investors:
Deposit local currency (DOP, ARS, COP)
Receive USDC in their Stellar wallet
Revenue recipients:
Convert USDC back to local currency
Withdraw at any MoneyGram location
alfredpay
LATAM banking rails for direct withdrawals to local bank accounts in:
Dominican Pesos
Argentine Pesos
Colombian Pesos
Stellar Disbursement Platform
SDF's open-source bulk payout infrastructure.
Asset operators can distribute revenue to all token holders in a single batch operation.
CHAPTER 3 — DEFI & CROSS-CHAIN
CCTP (Circle Cross-Chain Transfer Protocol)
Native 1:1 USDC transfer from EVM chains.
USDC is:
Burned on Ethereum/Polygon
Minted natively on Stellar by Circle
This enables Tokeshare to bring its existing EVM investor base onto Stellar.
Every dollar bridged becomes additional Stellar TVL.
Soroswap
AMM/DEX integration for secondary market trading.
Enables:
USDC/RWA liquidity pools
Token sales for existing holders
Open-market purchases for new investors
Swap widget embedded directly into the frontend.
Aquarius
Swap routing optimization across Stellar liquidity sources.
Reduces slippage and improves execution quality for RWA token trades.
USER JOURNEYS
Journey 1 — Non-Crypto LATAM Investor
Sign up via email (Privy)
Deposit cash at a MoneyGram agent
Receive USDC
Purchase fractional ownership in a real-world asset
Earn monthly revenue distributions
Withdraw via:
alfredpay (bank account)
MoneyGram (cash)
Examples of assets:
Rental apartments
Restaurants
Vehicles
Journey 2 — Crypto-Native EVM Investor
Bridge USDC from Ethereum or Polygon via CCTP
Connect a Stellar wallet using Wallets Kit
Purchase RWA tokens
Trade on the secondary market through Soroswap and Aquarius
Journey 3 — Asset Operator
Collect real-world revenue (rent, business income, fees)
Convert revenue into USDC
Distribute proceeds to token holders through Stellar Disbursement Platform
SUPPORTING INFRASTRUCTURE
Soroban Smart Contracts
RWA Sale Contract
Extension of the existing Soroban implementation
Already live at:
tokeshare.co/poc-stellar
RWA Token
SEP-41 implementation
OpenZeppelin Stellar compliance modules
Allowlist
Blocklist
Capped supply
Revenue Distribution Engine
Ported from the existing EVM BatchDistributor
Already live on Base
All contracts will be open-source under the MIT license.
WHY STELLAR
MoneyGram Access provides a unique fiat on/off-ramp network for LATAM investors
Native Circle USDC eliminates bridge risk
Sub-cent transaction costs make revenue distributions economically viable, even for hundreds of holders
Stellar combines payments, stablecoins, and asset issuance within a single ecosystem
WHAT TOKESHARE BRINGS TO STELLAR
Real TVL backed by physical assets
Real transaction volume from purchases and revenue distributions
Real users in LATAM markets
The first RWA use case combining:
MoneyGram
Privy
Soroswap
Stellar Disbursement Platform
within a single end-to-end investment product.
Requested Budget
$133.0K
Traction Evidence
Tokeshare is already operating live real-world asset tokenization infrastructure across Base, Polygon, and Ethereum, demonstrating the complete lifecycle of RWA issuance, investor onboarding, ownership tracking, and revenue reporting.
Unlike most RWA platforms focused on financial products such as treasuries or synthetic exposure, Tokeshare focuses on operational, revenue-generating assets from the real economy. The protocol tokenizes businesses and physical assets whose returns are generated by real-world activity, such as customers purchasing goods and services, tenants paying rent, or infrastructure generating recurring revenue.
Its first live asset, French Tacos Las Terrenas in the Dominican Republic, represents a $31,250 tokenized operating business on Base. To date, 722.05 of 1,000 shares have been sold to investors, representing $22,564.06 in on-chain investment. Token holders benefit from transparent ownership records and investor reporting linked to real business performance and cash-flow generation.
Across its live deployments, Tokeshare currently manages more than $43,945 in tokenized and asset-backed products. The protocol has also established a strategic partnership with BIM Exchange to support asset distribution, visibility, future liquidity, and the exploration of lending use cases using tokenized real-world assets as collateral.
Beyond its live assets, Tokeshare has already built an active origination pipeline of real-world assets scheduled for future tokenization. This pipeline includes Angel Cœur Caribe, a real estate development project currently progressing through technical planning and administrative approvals, and Résidence Monopoly Villa 8 Village, a residential real estate project where commercial demand has already been partially validated through early purchase commitments. Tokeshare is also evaluating additional revenue-generating businesses, including Crespa and Athela Café, two hospitality concepts located in the Dominican Republic. This pipeline demonstrates Tokeshare's ability not only to tokenize assets, but also to source, structure, and onboard new real-world opportunities, providing a clear path for future TVL growth, investor activity, and transaction volume on Stellar.
Having already validated market demand and operational execution, Tokeshare is now building its next phase on Stellar. The project will leverage Stellar's stablecoin ecosystem, payment infrastructure, and low-cost settlement layer to enable programmable ownership, transparent revenue distribution, and cross-border investment into revenue-generating assets located in emerging markets.
Evidence & Links
French Tacos Las Terrenas (Live Tokenized Business)
https://tokeshare.co/marketplace/other/french-tacos
https://basescan.org/token/0xB48F4d5E455a6d67f26FE364a201F51FF71aaB26
https://tokeshare.co/French_Tacos_LT_SRL_Official_Investor_Report_Q1_2026_EN.pdf
https://frenchtacos.info/
BIM Exchange Partnership
https://x.com/Tokeshare/status/2057511492958703970
Additional Live Assets
Gold / PAX Gold:
https://tokeshare.co/marketplace/commodities/Gold
TMC Index:
https://tokeshare.co/marketplace/stock-etf/tmc
Future Asset Pipeline
Angel Cœur Caribe:
https://angelcoeurcaribe.com/
Athela Café:
https://drive.google.com/file/d/1XdA2j4mVaOVUQ5q9vX_8O3J5MkFHza_i/view
Crespa: (supporting document attached)
Résidence Monopoly Villa 8 Village: (project documentation available upon request)
Tokeshare
https://tokeshare.co
https://x.com/Tokeshare
Tranche 1 (Deliverable Roadmap) - MVP
[Deliverable 1.1] Soroban RWA Infrastructure (Sale Contract + Token)
Description
Build the core Soroban infrastructure powering Tokeshare's Stellar marketplace. Extend the existing Soroban sale contract (already live at
tokeshare.co/poc-stellar
) into a multi-asset issuance system supporting asset creation, token sales, investor management, refunds, and lifecycle controls. Deploy a compliant SEP-41 RWA token with allowlist, blocklist, capped supply, pause, freeze, and burn capabilities using OpenZeppelin Stellar components.
How to measure completion
Multi-asset issuance system deployed on Stellar testnet
Multiple tokenized assets created and managed through the protocol
SEP-41 RWA token supporting compliance and supply controls
Public GitHub repository with source code and documentation
Automated test suite covering issuance, sales, transfers, and compliance features
Estimated completion date
August 1, 2026
Budget
$26,200
[Deliverable 1.2] Investor Onboarding Layer (Wallets Kit + Privy)
Description
Build the investor onboarding layer enabling both crypto-native and non-crypto users to access Tokeshare on Stellar. Extend the existing Wallets Kit integration to support multi-asset investing, portfolio management, and automated trustline handling. Integrate Privy to provide embedded wallets through email and social login, removing wallet setup complexity for mainstream investors.
How to measure completion
Wallets Kit operational with Lobstr, Freighter, and xBull
Multi-asset purchase flow functional on testnet
Automated trustline management working
Portfolio view displaying investor holdings
Privy embedded wallet creation functional via email/social login
Successful investments executed through both onboarding paths
Estimated completion date
August 22, 2026
Budget
$12,400
Tranche 2 (Deliverable Roadmap) - Testnet
[Deliverable 2.1] Revenue Distribution Engine + Data Infrastructure
Description
Build Tokeshare's revenue distribution infrastructure on Stellar. Develop a Soroban-based distribution engine enabling operators to distribute USDC revenue to token holders through a scalable claim mechanism. Build the supporting data layer, including event indexing, state reconstruction, and APIs powering investor dashboards, portfolio tracking, and distribution history.
How to measure completion
Revenue distribution engine deployed on Stellar testnet
Multiple USDC distribution cycles executed successfully
Token holders able to claim distributions through the application
Public repository with source code and documentation
Event indexer processing on-chain activity in near real-time
API operational and serving portfolio and distribution data
Estimated completion date
September 12, 2026
Budget
$20,800
[Deliverable 2.2] Fiat Access & Distribution Layer
Description
Integrate Stellar-native fiat access and payout infrastructure. Connect MoneyGram for cash deposits and withdrawals, alfredpay for local bank transfers, and Stellar Disbursement Platform for bulk USDC distributions. Deliver a complete investment lifecycle allowing users to move from local currency to tokenized assets and back to fiat.
How to measure completion
MoneyGram cash-in and cash-out flows operational
alfredpay withdrawal flow functional for at least one LATAM currency
Stellar Disbursement Platform integrated and tested
Revenue distributions executed to multiple token holders
End-to-end user journey validated: fiat deposit → asset purchase → revenue distribution → fiat withdrawal
Estimated completion date
October 3, 2026
Budget
$24,600
Tranche 3 (Deliverable Roadmap) - Mainnet
[Deliverable 3.1] Mainnet Launch & Liquidity Layer
Description
Deploy Tokeshare's complete infrastructure on Stellar mainnet. Integrate CCTP to onboard USDC liquidity from EVM ecosystems, launch secondary market trading through Soroswap and Aquarius, and tokenize the first real-world asset on Stellar. Execute the first live USDC revenue distribution to token holders.
How to measure completion
CCTP transfer successfully executed from at least one EVM chain
Soroswap liquidity pool created and operational
Aquarius routing integrated and functional
All contracts deployed on Stellar mainnet
At least one real-world asset tokenized on Stellar
Real investors holding tokenized assets
First USDC revenue distribution executed
Contract addresses published on Stellar Expert
Estimated completion date
November 10, 2026
Budget
$34,800
[Deliverable 3.2] Production Platform & Ecosystem Metrics
Description
Launch the production Tokeshare platform with all integrations live on mainnet. Deliver complete investment journeys for both crypto-native and fiat users, including onboarding, investing, revenue collection, secondary market trading, and withdrawals. Publish ecosystem impact metrics and technical documentation.
How to measure completion
Production platform live on
tokeshare.co
Fiat onboarding flow operational (Privy + MoneyGram)
Crypto-native onboarding flow operational (Wallets Kit + CCTP)
Secondary market trading operational (Soroswap + Aquarius)
Revenue distributions operational through Stellar Disbursement Platform
Fiat withdrawals operational through alfredpay
Public metrics dashboard displaying TVL, holders, transactions, and volume
Technical documentation published
Estimated completion date
November 24, 2026
Budget
$14,200
Team
Damian Py — CEO & Co-Founder
Entrepreneur and engineer with experience across blockchain, industrialization, clean energy, and hardware startups. Former CTO & Co-Founder of Daan Technologies, where he helped scale the company from €0 to €10M in annual revenue. Leads Tokeshare’s strategy, asset structuring, and RWA development.
https://www.linkedin.com/in/damianpy/
Adrien Gonçalves — CTO
Blockchain developer specialized in RWA tokenization, EVM ecosystems, protocol integrations, and Soroban smart contracts. Leads Tokeshare’s technical architecture and smart contract infrastructure.
https://www.linkedin.com/in/adrien-gon%C3%A7alves/
https://github.com/adgclvs
Nolan Pestre — Blockchain Developer
EVM and Soroban developer focused on smart contracts, token issuance, asset management, and on-chain revenue distribution.
https://www.linkedin.com/in/nolan-pestre-050b58293/
https://github.com/npnono
Lionel Pestre — Infrastructure & Energy Advisor
Former EDF expert with 25+ years of experience in energy infrastructure, construction, and operational asset evaluation. Supports asset assessment and sustainability analysis.
https://www.linkedin.com/in/lionel-pestre-1265aa94/
Philippe Guelfucci — Asset & Project Operations
Architecture specialist based in the Dominican Republic with 40+ years of experience in project operations and asset development. Supports asset evaluation and project structuring.
https://www.linkedin.com/in/philippe-guelfucci-3367b6103/
Aridio Guzmán — Legal & Compliance
https://drlawyer.com/
Damian
```
