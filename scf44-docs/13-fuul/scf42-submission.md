Source: https://communityfund.stellar.org/submissions/reczUrUF68Gmj3QYe

# Fuul — Unlocking DeFi Incentives on Stellar (SCF #42, Build, requested $150,000, Not Awarded)

Video: https://youtu.be/Tzas5VtwAz4 · Website: https://www.fuul.xyz

### Products & Services

Fuul is the incentives infrastructure platform for digital asset companies. It lets Institutions, DeFi, Fintechs, Exchanges (among others) pay tokens or points for any on-chain activity, in three steps:

1.  Define conversion events: Teams log into Fuul's incentives manager, point to a smart contract or integrated protocol, and select which on-chain activities they want to incentivize (swaps, deposits, holdings, looping strategies, etc.).
    
1.  Configure distribution rules: Teams choose how rewards are calculated and distributed: based on fees generated, TVL contributed, transaction volume, referral conversions, time-weighted positions, or custom combinations.
    
1.  Fund and go live: For on-chain payouts, teams deploy their own reward contract through the Fuul app with one click (they remain the sole admins, Fuul never holds custody of funds). Fuul tracks user activity across the configured conversion events and distributes points or tokens automatically based on the defined criteria.
    

  
The Stellar integration extends Fuul's full incentive stack natively to Stellar, giving projects building on Stellar access to the same growth infrastructure used by Coinbase, Kraken, dYdX, and Morpho on other chains.  

This is how we will be expanding our product onto Stellar, in three layers:  

1.  Adding Stellar to the Fuul stack
    

  
Full Platform Extension (Webapp, APIs, SDK)  
Stellar is added as a first-class chain across Fuul's entire platform: the REST API supports Stellar addresses and assets, the JavaScript SDK handles Stellar transaction signing for claim flows, and the Fuul app allows program configuration, trigger setup, and analytics.

Impact: Stellar projects gain immediate access to Fuul's full incentive suite: affiliate programs, DeFi liquidity rewards, referral systems, trading competitions, cashback programs, and points programs.

  
Stellar Wallets Kit Integration

Fuul's white-label claiming pages and referral hubs integrate with Stellar Wallets Kit, supporting wallets for authentication and reward claiming. Users connect their Stellar wallet, verify their allocation proof, and claim their reward tokens directly from the Soroban contract, either through Fuul's hosted claiming page or any web app integrating Fuul's APIs/SDKs.

Impact: Projects get a fully white-labeled, production-grade claiming experience without building any wallet infrastructure (and any team already using Fuul's APIs can extend the same flow to their own frontend)  

1.  Stellar Conversion Triggers
    

Aquarius DEX Integration

A pre-built trigger connector indexes LP provision and trading volume events on Aquarius, normalizing them into Fuul's event schema. Projects configure rules directly on Fuul's app. For example, "reward wallets that provide >$500 of USDC/XLM liquidity for 7+ days." Stellar's ledger is the source of truth for event verification.

Impact: DeFi protocols and ecosystem teams can incentivize Aquarius liquidity in minutes, accelerating swap volume and TVL growth across Stellar's DEX layer.

  
Blend v2 Integration

A pre-built trigger connector reads Blend v2's index to track lending supply and borrow positions per wallet. Enables programs that automatically reward lenders for supplying assets or borrowers for sustained utilization.

Impact: Lending protocols on Stellar can run automated supplier and borrower incentive programs without any custom engineering.

  
Stellar Token Holders

A pre-built trigger connector reads the holding positions for any specific token on the Stellar Network, allowing its issuer to reward users as they wish. Many times, these token holder triggers are also used to track DeFi receipt tokens.  
  
Impact: Any Stellar team can incentivize users to hold their tokens.  

1.  Soroban Reward Distribution Contracts
    

These contracts will handle budget deposits and reward distribution, natively on Stellar. Projects can deposit XLM, stablecoins (e.g. Stellar-issued USDC), or any other SEP-41 token into the contract; Fuul's engine computes per-wallet allocations and users claim directly on-chain using their Stellar wallet. Contracts will be open-sourced on GitHub.

Impact: Any Stellar project can run fully on-chain, trustless incentive programs without building or managing distribution infrastructure.

### Requested Budget



### Traction Evidence

Fuul is live in production and operating at scale across EVM and Solana chains:

-   **164** incentive programs launched since 2024
    
-   **36** active clients paying monthly retainers
    
-   **146M+** incentivized transactions processed
    
-   **12M+** unique wallets rewarded on-chain
    
-   **$2.6M+** in token rewards distributed on-chain (excludes points programs)
    
-   **30+** native protocol integrations (DEXs, lending, liquidity, staking, trading, social)
    
-   **9x MRR growth** in 2025, expanding from early DeFi adopters to enterprise clients
    

  
**Notable clients:** Coinbase, Kraken, Morpho, dYdX, Belo Neobank, Sentora, Nado, Euler (among others)

**Clients actively looking to deploy incentives on Stellar:** Etherfuse (POC: Andres Salcedo), M1X Global (POC: Jeremy Coffey), Sentora (POC: Lucas Outumuro)

  
**Case studies:** <https://www.fuul.xyz/case-studies>

**Platform:** <https://app.fuul.xyz>

  
Fuul was accelerated by a16z Crypto CSX (2023 cohort) and bootstrapped ever since. The core infrastructure (event indexing, incentive engine, distribution contracts, analytics) is already production-hardened across multiple chains. This grant funds the Stellar-specific extension of proven infrastructure.

### Tranche 1 (Deliverable Roadmap) - MVP

**Tranche #1: Full Platform Extension to Stellar**

**Deliverables:**

1.  **REST API: Stellar Support:** Stellar added as a supported chain\_id across all relevant endpoints: program creation, event ingestion, allocation calculation, and distribution. Endpoints accept Stellar wallet addresses and Stellar asset formats.
    
1.  **Incentives Setup: Stellar Chain:** Stellar chain selection available in Fuul's program setup UI. Supports trigger configuration and reward token selection (XLM, stablecoins, or any SEP-41 Stellar token).
    
1.  **SDK: Stellar Transaction Signing:** JavaScript/TypeScript SDK updated to support Stellar transaction signing for claim flows. The API endpoint returns the claim data; the client application constructs and submits the transaction.
    
1.  **Analytics: Stellar Metrics:** Stellar-specific metrics (active wallets, reward volume by token, protocol-level attribution) surfaced in program analytics dashboards.
    

  
**How to measure completion:**

-   API reference updated; reviewer can receive valid API responses for incentives programs on Stellar
    
-   Reviewer can create a complete Stellar incentive program in the staging Fuul dashboard without writing any code
    
-   SDK updated in npm; code example demonstrating a Stellar claim flow is publicly available
    
-   Reviewer can view analytics for a test Stellar program in the Fuul dashboard
    

  
**Budget:** $30,000

### Tranche 2 (Deliverable Roadmap) - Testnet

**Tranche #2: Protocol Trigger Integrations**

**Deliverables:**

1.  **Aquarius DEX Trigger Connector:** Connector indexing Aquarius liquidity pool activity on Stellar's ledger: LP token minting/burning and swap volume per wallet. Normalized into Fuul's event schema. Configurable rules in Fuul's dashboard (minimum liquidity threshold, time-weighted position requirements, volume targets).
    
1.  **Blend v2 Lending Trigger Connector:** Connector reading Blend v2's index to track supply and borrow positions per wallet. Enables projects to reward lenders and borrowers automatically based on position size, duration, or utilization thresholds.
    
1.  **Stellar Token Holders Trigger Connector:** Connector reading holding positions for any specific token on the Stellar Network. Allows token issuers to configure reward programs for holders directly from Fuul's dashboard.  
    

**How to measure completion:**

-   Live connector running against Stellar testnet, processing Aquarius events. Reviewer can create a test program using Aquarius as a trigger and verify that test wallet actions are captured and allocated rewards
    
-   Live connector running against Stellar. Test program using Blend v2 triggers; test wallet lending activity captured and allocated rewards verifiably
    
-   Live connector running against Stellar. Test program using token holding as a trigger; test wallet balances captured and rewards allocated verifiably  
    

**Budget:** $45,000

### Tranche 3 (Deliverable Roadmap) - Mainnet

**Tranche #3: Soroban Contracts + Mainnet Launch**

**Deliverables:**

1.  **Soroban Reward Distribution Contracts: Testnet + Mainnet Deployment:** Non-custodial reward distribution contracts deployed to Stellar mainnet. Support: (a) budget deposit in XLM, stablecoins, or any SEP-41 token, (b) cryptographic proof creation for user claims, (c) trustless claim with wallet signatures. Built on Soroban's auth framework and SEP-41 token interface.
    
1.  **Smart Contracts Open-Sourced:** Full contract code published to a public GitHub repository under MIT license.
    
1.  **End-to-End Claiming Flow on Mainnet:** Users connect a Stellar wallet via Stellar Wallets Kit on a live Fuul claiming page or any white-label web app, view earned rewards, and claim from the Soroban contract on mainnet.
    
1.  **Public Developer Documentation:** Integration guide and developer docs for Stellar projects: contract reference, API docs for Stellar-specific endpoints, SDK usage examples, and step-by-step guide for launching a Stellar incentive program.
    

  
**How to measure completion:**

-   Public mainnet contract addresses on Stellar explorer. Successful end-to-end transactions: deposit → allocation → claim, with real wallets and mainnet tokens
    
-   Public GitHub repo link with commit history and MIT license file
    
-   Reviewer can connect a wallet on a live claiming page and observe a verifiable on-chain claim transaction on Stellar mainnet explorer
    
-   Publicly accessible documentation URL covering all Stellar-specific integration surfaces
    

  
**Budget:** $60,000

### Team

Fuul is a team of 10, led by two complementary founders:\n\nLeandro Schlottchauer (CEO) — 3rd time founder with deep expertise in performance marketing and incentive systems. Co-founded Trocafone (BackMarket for LATAM) and BullMetrix (one of the top PPC marketing agencies in LATAM). MIT MBA 2022. Leandro brings a decade of experience designing and scaling user acquisition and rewards programs, which directly shaped Fuul's product vision. LinkedIn: <https://www.linkedin.com/in/leandrosch/>\n\nAndre Brener (CTO) — Solidity and blockchain engineer, 1st place winner at ETH Buenos Aires Hackathon 2018. Started in data engineering within AdTech before transitioning fully to web3. Andre architected Fuul's multi-chain smart contract infrastructure and has shipped production contracts across EVM and Solana. LinkedIn: <https://www.linkedin.com/in/andre-brener-2b026671/>\n\nThe rest of the team brings complementary skills across backend and frontend development, data engineering, and customer success.\n


### Links

- https://app.fuul.xyz
- https://www.fuul.xyz/case-studies
