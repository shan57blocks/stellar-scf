Source: https://communityfund.stellar.org/submissions/recdYL3NzcAk4co1G

# Embedded Collective Investment Platform (SCF #43, Build, Prescreen Failed)

Links on the page:
- https://collectiveinvestment.escalahq.com
- https://docs.google.com/document/d/1w6gW5SwUEl7QZylIBi1bp34vaEAPZmL1
- https://www.youtube.com/watch?v=0MvRisdFAjA

## Products & Services

This submission funds the development of the Escala HQ B2B "Collective Investment" FaaS infrastructure, explicitly scoped and tailored for our Go-To-Market pilot with the United Nations (UNDP). We are building three core pillars:  

**1. Soroban Core Contracts:** A Treasury Contract handling USDC escrows and milestone logic, paired with a Governance Contract issuing Soulbound Tokens (SBTs). SBTs are a strict UNDP compliance requirement to enforce non-transferable, 1-to-1 democratic voting in vulnerable communities, preventing Sybil attacks.  

**2. Integration-Heavy Middleware:** A robust enterprise backend that orchestrates the Stellar ecosystem:

-   **Firebase Authentication** (Google Cloud Identity Platform) **(OIDC):** For secure B2B tenant identity management.
    
-   **MoneyGram Access API:** Serving as the fiat-to-USDC on-ramp for capitalizing the community funds.
    
-   **Stellar Disbursement Platform (SDP):** Triggered automatically by Soroban to execute bulk, milestone-based USDC payouts to local vendors once community proposals pass.  
    

**3. Cloud Studio (B2B Integration Accelerator):** A developer-focused environment leveraging the Model Context Protocol (MCP). It is not an end-user gimmick, but a Web3 DevTool that allows NGOs and cooperatives to seamlessly embed these Stellar primitives (SDP, MoneyGram, Soroban) into their legacy legacy systems in weeks instead of months.

## Requested Budget

$70.0K

## Traction Evidence

Our traction is built on a live operational baseline, rigorous institutional validation, and industry recognition.  

**1. Institutional Validation & UNDP Pilot (Our B2B Go-To-Market):** > The core "Collective Investment" primitive we are building on Soroban was co-created, battle-tested, and validated under the United Nations (UNDP) SDG Blockchain Accelerator. We successfully graduated from the program and tested the end-to-end flow in a live Demo Day in a municipality in Guatemala.

-   **Verifiable Evidence:** [UNDP blog post](https://www.undp.org/es/guatemala/noticias/pnud-guatemala-impulsa-soluciones-basadas-en-blockchain-para-fortalecer-el-desarrollo-local)  
    

**2. Live B2C Baseline (Amero):** > The fiat-crypto integration risk is already mitigated. Escala HQ is the B2B evolution of our production-grade B2C platform, Amero, which has successfully integrated MoneyGram/Circle and serves over 15,000 active users across 8 countries.

-   **B2C Live Platform:** <https://amero.lat>  
    

**3. Industry Recognition:** > We are recognized for our ability to bridge TradFi and Web3 in LATAM, having won the Fintech Americas Awards for 3 consecutive years in the DeFi/Web3 Category (2023, 2024, 2025).

-   **Verifiable Evidence: Look** _in the DeFi category_ **->** [https://www.fintechamericas.co/](https://www.fintechamericas.co/ganadores-premios-2025)

## Tranche 1 (Deliverable Roadmap) - MVP

Testnet Smart Contracts & Basic Setup.  
**Requested Budget:** $14,000 USD  
**Description:** Development of the simplified foundational Soroban smart contracts (Treasury USDC escrow and Voting SBTs for non-transferable governance). Setup of Firebase Authentication (Google Cloud Identity Platform) as the OIDC provider for secure B2B tenant authentication. **Success Metric / Verification:** Links to the deployed Treasury and Governance contracts on Stellar Expert (Testnet). A GitHub repository containing the Rust code. A live staging URL demonstrating a successful OIDC login flow using Firebase Authentication.

## Tranche 2 (Deliverable Roadmap) - Testnet

MoneyGram, Stellar wallet connection & SDP Integrations.  
**Requested Budget:** $21,000 USD  
**Description:** Backend middleware implementation establishing the explicit API connections for our Integration Track partners. This includes integrating the MoneyGram Access API for fiat-to-USDC capitalization, Stellar wallet connection to smart contracts and the Stellar Disbursement Platform (SDP) for executing milestone-based vendor payouts. **Success Metric / Verification:** A live Swagger/OpenAPI link. A video demonstration on Testnet showing the end-to-end flow: simulating a fiat deposit via MoneyGram API logic, locking it in the Soroban Escrow, and successfully triggering a bulk payout via the SDP API upon milestone validation.

## Tranche 3 (Deliverable Roadmap) - Mainnet

Mainnet Launch & UNDP Pilot Readiness.  
**Requested Budget:** $28,000 USD.  
**Description:** Final QA, removal of any remaining bottlenecks, and the complete deployment of the simplified Escala B2B infrastructure to the Stellar Mainnet, specifically tailored for the Go-To-Market pilot with our UNDP partners in Guatemala. **Success Metric / Verification:** Mainnet contract addresses verified on Stellar Expert. Production-ready API endpoint URLs. A final video report showing a complete Mainnet transaction cycle ready for NGO usage.

## Team

We bring deep expertise bridging TradFi and Web3 in LATAM. We previously built Amero (our B2C vertical), scaling it to 15,000+ users across 8 countries and winning the [Fintech Americas DeFi award](https://www.fintechamericas.co/ganadores-premios-2025) 3 consecutive years (2023-2025). This exact team built the "Collective Investment" primitive that won the UN Hackathon and graduated from the UNDP Accelerator 2026.  

**Rafael Osiris Rodriguez (CEO)** <https://www.linkedin.com/in/rafaelosiris/> Serial fintech entrepreneur. Rafael leads our B2B vision, regulatory compliance (FinCEN MSB), and high-level institutional alignment, including managing our critical GTM distribution partnership with the UNDP.  

**Samuel Peralta (CTO)** <https://www.linkedin.com/in/samuelperaltamateo/> Lead architect of our Soroban smart contracts and B2B PaaS. A technical graduate of the UNDP Accelerator, Samuel built our production-grade integrations with Tier-1 ecosystem partners (MoneyGram, Circle USDC, Bridge, etc).  

**Francisco Rodriguez (CBDO)** <https://www.linkedin.com/in/frpenalo/> Drives business development and GTM scalability. Francisco secured our operational partnerships with MoneyGram and others partners, and leads our enterprise, cooperative, and NGO onboarding pipeline via UNDP channels.
