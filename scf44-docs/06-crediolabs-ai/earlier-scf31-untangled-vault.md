Source: https://communityfund.stellar.org/submissions/rec5hF8af5tfhSlT2 (SCF #31, Untangled, awarded $150.0K, category Build). Text extracted from the public submission page on 2026-09-24.

# Untangled: Enabling a secondary market for RWAs on Stellar with Untangled Vault

## Products & Services

Untangled Vault is an automated, non-custodial, data-driven asset management protocol enabling anyone to set and execute strategies to fit investor risk/return appetite. Untangled Vault is powered by Untangled pool, an asset tokenization protocol focusing on RWA private credits and Credio, a risk oracle providing machine learning model inferences and onchain monitoring of RWA. Untangled Vault is an EVM compatible smart contract platform. This application is to deploy Untangled Vault and necessary supporting components of Untangled Pool and Credio on Stellar. This necessitates the conversion of EVM contracts to Soroban as well as adapting and building all relevant technical components. For details please refer to the link [https://docs.google.com/document/d/1p9XyJo8uuvMboWFaF-QS4KgWhx1ZSjqsytIxDyDDPKo/edit](https://docs.google.com/document/d/1p9XyJo8uuvMboWFaF-QS4KgWhx1ZSjqsytIxDyDDPKo/edit)

## Soroban

Yes

## Requested Budget

$$150.0K

## Success Criteria

Stellar is home to major RWA projects like Franklin Templeton's Benji and Wisdom Tree, but its DeFi ecosystem is still in its early stages according to [Dune](https://dune.com/scoffie/stellar?utm_medium=email&_hsenc=p2ANqtz-8sZJMo2DPSBx_uZtMgpu-gyu9eEVe7E5EYaNBnfEVVnOXkGqG9v8rKvdtO3zXZjfKJSvYoUTaYWi-9oG0ciIU78EJEIX0Tzs0qxgYJyOKoKd_YqDI&_hsmi=96780372&utm_content=96780372&utm_source=hs_email) and defillama dashboard [https://defillama.com/chain/Stellar](https://defillama.com/chain/Stellar). This presents a unique opportunity for Untangled Finance to make a impact, especially with the launch of the Soroban smart contract platform. RWAs (on any chain including Stellar) face challenges in moving beyond a mere, close-loop tokenisation to a landscape where tokens have more utility on secondary markets e.g. trading, used as collateral in lending markets or composed into products that fit investor risk, return and usage preferences. At Untangled and Credio, our risk modelling and oracle service, we believe these boils down to Product  * a liquid product that can achieve balance between risk, return and liquidity such as tokenized MMF, index funds or short term treasuries * a product that have a low barrier to entry e.g. minimum investment size or KYC particularly for emerging markets such as Brazil, Mexico, Colombia, Turkey, Ukraine and Nigeria * Stellar infrastructure primitives that can support such product innovations. Legal  * Token is tradable or used as collateral in third party systems after a possible short lock-up period * Minimum KYC requirement on secondary market beyond some compulsory require e.g. sanction list whitelisting Liquidity  * Who can provide liquidity for the secondary market? Think of market makers and other large liquidity providers  * What is optimal incentive design and quantum from the protocols concerned and  Stellar? XLM incentive included. * Feasibility of non-custodial wallet distribution and/or DeFi secondary trading/staking.
20:T5

## Go-To-Market Plan

* Investor segment: We will target 3 groups * DAO Treasuries: Increasingly DAOs are allocating to RWAs. RWAs bring scale and stable and uncorrelated income for DAO, helping them to grow their treasury sustainably. However, some RWAs like private credits, whilst having higher yields and bigger real world impact, suffer from low liquidity ('no free lunch' principle in financial markets). With Untangled vault issuers can craft bespoke strategies for DAO treasuries, balancing yields and liquidity. We believe that this is a strong differentiation, currently lacking among RWA investment opportunities on Stellar. * Institutional users: In a balanced portfolio, RWA yields are a source of stability that institutional users seek. We will be working with partners in our network including Fasanara, a big player in the digital asset market, to approach institutional investors in allocating liquidity to Untangled Vault. * Retail users: Most currently RWA protocols (including our existing pool) set a high minimum investment amount. With Untangled vault we can set a lower minimum amount, removing a significant barrier to entry for many retail users. * Underlying product segments we will target * Pegged asset projects: Any pegged assets such as tokenized money market funds like Franklin Templeton's Benji token  * Stablecoin projects: likewise any stablecoins backed by RWAs, like USDC and staked tokens
2

## Traction Evidence

* Protocol developed and audited, launched on Celo mainnet - [Audit report](https://github.com/Verilog-Solutions/.github/blob/main/Audit/Untangle_Protocol_Audit/Untangled_FInance_Audit_Report.pdf) * Closed a strategic seed round led by Fasanara Capital, an institutional asset manager managing $4.5bn private credit portfolio with a network of 140 asset originators in 65 countries - [Coindesk article](https://www.coindesk.com/business/2023/10/10/tokenized-rwa-platform-untangled-goes-live-gets-135m-funding-to-bring-private-credit-on-chain/) * Sourced a pipeline of institutional-grade assets through partnership with Fasanara - [Fist pool launch coverage](https://www.coindesk.com/business/2024/05/02/tokenized-private-credit-platform-untangled-opens-its-first-usdc-lending-pool-on-celo/) * First pool live with $250k in TVL - [Link to pool TVL](https://app.untangled.finance/#/celo/pool-note/2) * Developed world’s first RWA decentralized credit oracle, Credio - [Whitepaper](https://credio.network/docs/whitepaper) * Credio is currently in pilot phase with a top credit rating agency - [Link to public dashboard on stablecoin depeg monitoring service](https://dune.com/untangled_credio/stablecoin-depeg-dashboard-summary)
26

## Tranche 1 (Deliverable Roadmap) - MVP

Deliverable 1: Untangled Credit Vault (MVP) (See detailed deliverables above) * What: Develop and deploy a single credit vault contract (ERC-4626 extension) on Soroban, supporting basic vault issuance and asset management for RWAs. * Measure: Successfully deploy the credit vault contract on Soroban testnet with basic functionality tested, including vault issuance and withdrawal epochs. * When: 4 weeks from award approval. Deliverable 2: Credio Oracle (MVP) (See detailed deliverables above) * What: Develop and deploy the first Credio oracle contract on Soroban, integrating basic ML-based credit risk scoring. * Measure: Test deployment of the credit risk scoring mechanism on Soroban testnet,. * When: 4 weeks from award approval. Budget: * $50k

## Tranche 2 (Deliverable Roadmap) - Testnet

Deliverable 1: Untangled Credit Vault (Testnet) (See detailed deliverables above) * What: Expand credit vault features to support more advanced asset management and cross-chain integration on the Soroban testnet. * Measure: Fully functional credit vault on the testnet with cross-chain module integration and basic user testing. * When: 6 weeks from award approval. Deliverable 2: Credio Oracle (Testnet) * What: Integrate ML models and complete credit scoring functionality * Measure: Successful deployment of oracle and inference mechanisms on the Soroban testnet with real-time risk scoring. * When: 6 weeks from award approval. Budget: * No XLM reward, but access to Stellar LaunchKit for audits and infrastructure credits.

## Tranche 3 (Deliverable Roadmap) - Mainnet

Deliverable 1: Untangled Credit Vault (Mainnet) * What: Launch the credit vault contract on the Stellar mainnet, integrating multisig, other cross-chain transaction management, and user interface support. * Measure: Full deployment on the Stellar mainnet, with all main features functional and available for user interaction. * When: 12 weeks from award approval. Deliverable 2: Credio Oracle (Mainnet) (See detailed deliverables above) * What: Deploy the Credio Oracle on the Stellar mainnet * Measure: Full deployment of the Credio Oracle on Stellar mainnet. * When: 12 weeks from award approval. Budget: * $100,000k - covering final development and mainnet deployment for both Vault and Credio

## Team

* Untangled's co-founders are Manrui Tang (Ms) and Quan Le (Mr) * Met at PwC while working on the impact of the 2008 Global Finance Crisis. * Both grew up in Asia but worked globally thus developed unique insights into democratizing access to capital around the world. * Passionate about building blockchain solutions to address the needs of the real world economy. Single focus on tokenization since the first wave in 2017. Manrui Tang, Co-Founder * 10y in M&A, due diligence in London with degrees from Imperial, LSE * Building in blockchain since 2017  * Focusing on funding, partnerships and distribution  Quan Le, Co-Founder * 15y in M&A, strategy, due diligence before founding an agtech advisory firm in Africa * Building in blockchain since 2017 * Focusing on strategy, product and engineering
