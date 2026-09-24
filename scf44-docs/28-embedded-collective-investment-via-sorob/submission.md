# Embedded Collective Investment via Soroban: Embedded Collective Investment

Source: https://communityfund.stellar.org/submissions/recTLN53hfYLoLT78 (SCF #44, awarded $70.0K, category: End-User Application)

- Website: https://collectiveinvestment.escalahq.com
- Architecture doc: https://docs.google.com/document/d/1w6gW5SwUEl7QZylIBi1bp34vaEAPZmL1/edit?usp=sharing&ouid=109175901284957979851&rtpof=true&sd=true

## Links in the submission

- https://docs.google.com/document/d/1w6gW5SwUEl7QZylIBi1bp34vaEAPZmL1/edit?usp=sharing&ouid=109175901284957979851&rtpof=true&sd=true
- https://github.com/escala-dev/collective-investment-api
- https://medium.com/stellar-community/stellar-community-fund-2025-impact-report-6f6c6361aaca

## Submission text (as published)

```text
Products & Services
This submission funds the development of the Escala HQ B2B "Collective Investment" FaaS infrastructure, explicitly scoped and tailored for our Go-To-Market pilot with the United Nations (UNDP). We are building three core pillars:
1. Soroban Core Contracts: A Treasury Contract handling USDC escrows and milestone logic, paired with a Governance Contract issuing Soulbound Tokens (SBTs). SBTs are a strict UNDP compliance requirement to enforce non-transferable, 1-to-1 democratic voting in vulnerable communities, preventing Sybil attacks.
2. Integration-Heavy Middleware: A robust enterprise backend that orchestrates the Stellar ecosystem:
Firebase Authentication (Google Cloud Identity Platform) (OIDC): For secure B2B tenant identity management.
MoneyGram Access API: Serving as the fiat-to-USDC on-ramp for capitalizing the community funds.
Stellar Disbursement Platform (SDP): Triggered automatically by Soroban to execute bulk, milestone-based USDC payouts to local vendors once community proposals pass.
3. Cloud Studio (B2B Integration Accelerator): A developer-focused environment leveraging the Model Context Protocol (MCP). It is not an end-user gimmick, but a Web3 DevTool that allows NGOs and cooperatives to seamlessly embed these Stellar primitives (SDP, MoneyGram, Soroban) into their legacy legacy systems in weeks instead of months.
Requested Budget
$70.0K
Traction Evidence
Our traction is built on a live operational baseline, rigorous institutional validation, and industry recognition.
1. Institutional Validation & UNDP Pilot (Our B2B Go-To-Market): > The core "Collective Investment" primitive we are building on Soroban was co-created, battle-tested, and validated under the United Nations (UNDP) SDG Blockchain Accelerator. We successfully graduated from the program and tested the end-to-end flow in a live Demo Day in a municipality in Guatemala.
Verifiable Evidence:
UNDP blog post
https://innovation.eurasia.undp.org/digital-payments-that-work-under-real-constraints/
https://medium.com/stellar-community/stellar-community-fund-2025-impact-report-6f6c6361aaca
 (in the topic of Social Good and Real-World Utility with our B2C app Amero).
2. Live B2C Baseline (Amero): > The fiat-crypto integration risk is already mitigated. Escala HQ is the B2B evolution of our production-grade B2C platform, Amero, which has successfully integrated MoneyGram/Circle and serves over 15,000 active users across 8 countries.
B2C Live Platform:
https://amero.lat
3. Industry Recognition: > We are recognized for our ability to bridge TradFi and Web3 in LATAM, having won the Fintech Americas Awards for 3 consecutive years in the DeFi/Web3 Category (2023, 2024, 2025).
Verifiable Evidence: Look in the DeFi category ->
https://www.fintechamericas.co/
https://x.fintechamericas.co/es/ganadores-2023-proyecto-amero-exchange
Tranche 1 (Deliverable Roadmap) - MVP
(Note: In alignment with the SCF Handbook structure, $7,000 [10%] corresponds to Tranche #0 upfront upon approval. The remaining $63,000 is strictly allocated across the 3 execution milestones below).
Tranche #1: MVP, Smart Contracts & OIDC Identity
Requested Budget: $14,000 USD
Description: Development of the core Soroban contracts and the enterprise multi-tenant identity baseline.
Budget Breakdown:
Soroban Smart Contracts Architecture & Dev (Treasury Escrow & SBTs) [80 hrs]: $5,000
Firebase OIDC Integration & Multi-tenant Setup [60 hrs]: $4,000
B2B Cloud Studio Dashboard MVP (Frontend/UI) [80 hrs]: $5,000
Success Metrics / Verification: 1. A public GitHub repository link containing the Rust contracts code. 2. Stellar Expert Testnet URLs showing the deployed Treasury and Governance contracts. 3. A video demo showing a successful Firebase OIDC login and dashboard access.
Tranche 2 (Deliverable Roadmap) - Testnet
Tranche #2: Integration Core (MoneyGram & SDP)
Requested Budget: $21,000 USD
Description: Middleware development to seamlessly connect Escala HQ to the mandatory Integration Track partners.
Budget Breakdown:
MoneyGram Access API Integration (Fiat-to-USDC on-ramp logic) [120 hrs]: $8,000
Stellar Disbursement Platform (SDP) Integration (Milestone-based Payouts) [100 hrs]: $7,000
Middleware orchestration, testing, and API routing [80 hrs]: $6,000
Success Metrics / Verification: 1. Updated public Swagger/OpenAPI documentation linked in GitHub. 2. A Testnet video demonstration showing an end-to-end B2B flow: simulated fiat deposit (MoneyGram logic), locking funds in the Soroban Escrow, and successfully triggering a payout via the SDP API upon milestone approval.
Tranche 3 (Deliverable Roadmap) - Mainnet
Tranche #3: Mainnet Launch & UNDP Pilot Readiness
Requested Budget: $28,000 USD
Description: Final QA, security optimizations, and Mainnet deployment of the B2B FaaS infrastructure tailored for the UNDP Go-To-Market pilot.
Budget Breakdown:
Mainnet Deployment & Network Configuration [60 hrs]: $5,000
End-to-End System QA & Load Testing [100 hrs]: $8,000
Developer Documentation & Cloud Studio Refinement [100 hrs]: $7,000
Pilot Onboarding Setup (UNDP compliance requirements) [100 hrs]: $8,000
Success Metrics / Verification: 1. Verified Mainnet contract addresses on Stellar Expert. 2. Production-ready API endpoints live. 3. Final video report showing a complete Mainnet transaction cycle ready for NGO/Enterprise usage.
Team
We bring deep expertise bridging TradFi and Web3 in LATAM. We previously built Amero (our B2C vertical), scaling it to 15,000+ users across 8 countries and winning the
Fintech Americas DeFi award
 3 consecutive years (2023-2025). This exact team built the "Collective Investment" primitive that won the UN Hackathon and graduated from the UNDP Accelerator 2026.  
Rafael Osiris Rodriguez (CEO)
https://www.linkedin.com/in/rafaelosiris/
 Serial fintech entrepreneur. Rafael leads our B2B vision, regulatory compliance (FinCEN MSB), and high-level institutional alignment, including managing our critical GTM distribution partnership with the UNDP.
Samuel Peralta (CTO)
https://www.linkedin.com/in/samuelperaltamateo/
 Lead architect of our Soroban smart contracts and B2B PaaS. A technical graduate of the UNDP Accelerator, Samuel built our production-grade integrations with Tier-1 ecosystem partners (MoneyGram, Circle USDC, Bridge, etc).
Francisco Rodriguez (CBDO)
https://www.linkedin.com/in/frpenalo/
 Drives business development and GTM scalability. Francisco secured our operational partnerships with MoneyGram and others partners, and leads our enterprise, cooperative, and NGO onboarding pipeline via UNDP channels.
Samuel Peralta
Francisco Rodriguez
Rafael Osiris Rodriguez
```
