# Offer-Hub: Financial Access for Global Freelancers

Source: https://communityfund.stellar.org/submissions/recQJGuWgzvNmv2sM (SCF #44, awarded $74.0K, category: Applications)

- Website: https://www.offer-hub.org/
- Architecture doc: https://www.offer-hub.tech/architecture

## Links in the submission



## Submission text (as published)

```text
Products & Services
Offer Hub
 is a freelance marketplace on Stellar where client payments lock into Soroban escrow and release only upon delivery, giving LATAM freelancers guaranteed, near-instant payments without intermediaries or custodial risk.
THE PROBLEM:
Fiverr charges up to 25% combined fees. Upwork charges 20% dropping to 5% at volume, plus client-side fees. For a LATAM freelancer earning $2,000/month, that is $300–$500 lost monthly before currency conversion costs and fund holds of up to 14 days. Over 650 million people in LATAM face this daily with no real alternative.
HOW IT WORKS:
Client creates an order → USDC locks into a Soroban smart contract.
Freelancer delivers → client approves.
Contract releases funds by code, no human intermediary.
Disputes resolved on-chain with fractional distribution.
Funds reach the freelancer's bank account (SPEI, Pix, PSE, Transfer 3.0) in minutes.
DIFFERENTIATION:
Upwork/Fiverr: up to 25% fees, custodial funds, 7–14 day holds.
Workana: same custodial model, no blockchain, no native off-ramp.
Crypto P2P: no escrow, no dispute resolution, no trust mechanism.
OfferHub: 4% flat fee, non-custodial on-chain escrow, same-day settlement.
REVENUE MODEL:
Two streams. SCF funding covers exclusively the two integration development costs (SWK, BlindPay).
Transaction fee: 4% on completed escrows in USDC. At 1,000 active users/$50K USDC monthly: ~$2,000/month.
Subscriptions (post-mainnet): Freelancer Pro $19/month, Client Business $49/month, Enterprise $199/month.
KNOWN CHALLENGES:
Wallet UX:
 OfferHub uses custodial server-side wallets with social login, which onboarded 103 users without crypto friction. SWK resolves this: users keep social login while gaining full non-custodial control.
Migration path:
 existing users migrate via a "Claim Your Wallet" flow using Stellar set_options to transfer signing authority to their own key — funds never move, active escrows complete under original keypairs. Server-side signing routes are removed at T3 and verifiable in the public repository diff.
Regulatory variance:
 BlindPay (FinCEN MSB, YC-backed) absorbs per-market compliance risk across 7 LATAM corridors.
XLM volatility:
 user funds denominated in USDC on-chain.
STELLAR USAGE (significant, not superficial):
Stellar is the sole settlement layer. Soroban contracts execute the escrow logic. USDC on Stellar is the payment asset. On-chain settlement detection runs in real time via Stellar RPC. The marketplace cannot operate without Stellar.
OPEN SOURCE:
OfferHub uses TrustlessWork's audited Soroban contracts (already open-source). Integration adapters for SWK and BlindPay will be published under MIT license as a mandatory Tranche #3 deliverable.
Requested Budget
$74.0K
Traction Evidence
Offer Hub
 officially opened to users on March 3, 2026. Every metric below was achieved organically in under 4 months — no paid advertising, no token incentives, no influencer campaigns.
ON-CHAIN ACTIVITY (Stellar Testnet):
15 Soroban escrow contracts created via TrustlessWork
4 on-chain transactions confirmed on Stellar blockchain
Registration → escrow conversion: 14.5% (103 users, 15 escrows)
All contracts publicly verifiable on Stellar Expert
CLIENT-SIDE DEMAND — ON-CHAIN EVIDENCE:
Each escrow requires a client to actively deposit USDC into a Soroban contract. The 15 escrows represent 15 clients who deposited real funds — not stated intent, on-chain action verifiable on Stellar Expert. The 4 confirmed transactions are complete purchase cycles: escrow created, work delivered, payment released.
USERS & WALLETS:
103 registered users each with a connected Stellar wallet — 1:1 ratio
20 freelancers actively listing services on the marketplace
50+ users on the commercial waitlist
OPEN SOURCE & COMMUNITY:
190+ active contributors on GitHub — organic, no incentives
259 repository forks
100+ members in the Telegram community
ECOSYSTEM RECOGNITION:
Recognized by Grantfox and Drips for technical quality and open-source contributions
Participated in the Open Source Wave within the Stellar ecosystem
Referred by active SCF community member (REF-alber-016)
GO-TO-MARKET:
Beachhead: Web3/DAO projects with USDC treasuries hiring LATAM technical talent — both sides already understand crypto, no onboarding friction.
Phase 1 (M1–6): product-led freemium, 0% commission on first 5 projects. Channels: crypto Telegram/Discord (40%), LATAM university/coworking partnerships (35%), content (25%).
Phase 2 (M6–12): subscription activation ($19/$49/$199/month tiers), enterprise B2B outreach as structural client-side demand source. Target: 8–12% freemium → paid vs. 5% industry benchmark.
Phase 3 (M12–18): geographic scale — BlindPay's 7 corridors already active, expansion is channel activation not new infrastructure.
VERIFICATION LINKS:
- Live Platform:
https://www.offer-hub.org
- Statistics:
https://www.offer-hub.org/stats
- GitHub:
https://github.com/OFFER-HUB
- Landing:
https://www.offer-hub.tech
- Twitter/X:
https://x.com/offerhub_
- Discord:
https://discord.gg/8hHJ9Fe5x
- LinkedIn:
https://www.linkedin.com/in/offer-hub-bb797a389
Tranche 1 (Deliverable Roadmap) - MVP
TRANCHE #1 — MVP: Stellar Wallets Kit Connection & Auth
OBJECTIVE:
 A user can connect their Stellar wallet to OfferHub and authenticate non-custodially on testnet, fully demonstrable for Tranche #1 review.
DELIVERABLE 1.1 — SWK Multi-Wallet Connection UI:
 Integration of Stellar Wallets Kit supporting Freighter, Lobstr, and xBull: wallet detection, selection, connection, disconnection, and account switching. USDC balance visible from connected wallet — no server-side keypair involved.
Cost:
 $7,500
Verification:
 video demo showing wallet selection modal on testnet with all 3 wallets functional. USDC balance confirmed against Stellar testnet explorer.
DELIVERABLE 1.2 — Wallet-Based Authentication:
 Challenge-response auth flow: backend issues a nonce, user signs with their wallet via SWK, backend verifies signature on-chain and issues session. Coexists with existing email/password login — users can sign in with email and connect wallet inside the platform, or sign in directly via wallet signature. Includes NestJS auth module refactor and sign-in UI update.
Cost:
 $8,500
Verification:
 end-to-end demo on testnet — user signs in with email → connects Freighter → signs nonce → wallet linked to account. Alternative demo: direct wallet-signature login. Both flows functional on testnet.
TRANCHE #1 TOTAL:
 $16,000
HOW TO MEASURE COMPLETION:
✓ SWK connected and live on testnet
✓ Freighter, Lobstr, xBull all functional
✓ Soroban transactions signed client-side confirmed
✓ Auth flow redesign complete and tested
✓ Demo video published showing both auth flows
Tranche 2 (Deliverable Roadmap) - Testnet
TRANCHE #2 — TESTNET: SOROBAN SIGNING + BLINDPAY
OBJECTIVE:
 Complete payment cycle verifiable on testnet — wallet connected via SWK signs the Soroban escrow, funds release, freelancer receives fiat in their local bank account.
DELIVERABLE 2.1 — SWK Soroban Client-Side Signing:
 Migration of create_escrow, release_escrow, refund_escrow, and resolve_dispute to be built and signed in the browser via SWK, eliminating server-side signing entirely. Each operation includes: Soroban transaction construction, on-chain simulation, user presentation for signing, and submission to the network.
Cost:
 $6,000
Verification:
 video demo — user signs create_escrow and release_escrow from Freighter in the browser with no server intervention. Soroban transaction IDs on testnet published to GitHub.
DELIVERABLE 2.2 — BlindPay USDC-to-Fiat Integration:
 BlindPay REST API integration: authentication, payout creation, 7 confirmed corridors (MX/SPEI, BR/Pix, CO/PSE, AR/Transfer 3.0, PE, CL, CR), confirmation webhooks with on-chain reconciliation, and frontend destination selector.
Cost:
 $6,000
Verification:
 end-to-end demo on testnet — escrow released → USDC routed → disbursement receipt from at least 3 distinct corridors. Confirmation webhook logged on server.
DELIVERABLE 2.3 — Off-Ramp Orchestration Layer:
 NestJS logic that detects escrow release on-chain and automatically routes to BlindPay based on freelancer country and preference. Includes error handling, retries, and disbursement status tracking.
Cost:
 $3,500
Verification:
 demo from 2.2 serves as verification — routing occurs without manual intervention.
DELIVERABLE 2.4 — Integration Testing & Verification: Test coverage for critical flows: SWK signing × 4 operations, BlindPay × 7 corridors. QA of webhooks and on-chain reconciliation. Failure scenario simulations: disbursement timeout, corridor rejection, retry logic.
Cost:
 $4,000
Verification:
 integration test suite in public repository. Failure simulation results documented.
TRANCHE #2 TOTAL:
 $19,500
HOW TO MEASURE COMPLETION:
✓ SWK client-side signing live for all 4 escrow operations
✓ BlindPay end-to-end verified in minimum 3 corridors
✓ Off-ramp routing automated with no manual intervention
✓ Integration test suite published in public repository
Tranche 3 (Deliverable Roadmap) - Mainnet
TRANCHE #3 — MAINNET LAUNCH
OBJECTIVE:
 OfferHub live on Stellar mainnet with 2 integrations active (SWK + BlindPay), running on current Stellar stack, with integration adapters open-sourced.
DELIVERABLE 3.1 — Horizon → Stellar RPC Migration:
 Replace Horizon dependency with Stellar RPC for escrow release detection and Soroban contract interaction. Required for correct mainnet operation with Soroban contracts.
Cost:
 $5,500
Verification:
 escrow release detection working on mainnet via Stellar RPC. Integration test confirming Horizon is no longer a critical dependency.
DELIVERABLE 3.2 — Mainnet Deployment:
 Deployment of both integrations (SWK + BlindPay) on mainnet with production credentials. End-to-end connectivity verification with each provider.
Cost:
 $4,500
Verification:
offer-hub.org
 accessible and functional with SWK and BlindPay active on Stellar mainnet. Initial transactions verifiable on Stellar Expert.
DELIVERABLE 3.3 — Live Production Validation Framework:
 Automated framework that executes and verifies the complete escrow → fiat cycle on mainnet reproducibly. Results — transaction IDs, disbursement statuses, confirmation times — published automatically to
offer-hub.org/stats
. Minimum 3 BlindPay corridors covered.
Cost:
 $5,000 (Core Development): framework construction ~35 hrs ($4,200) + results publishing pipeline ~7 hrs ($800).
Verification:
 public repository with framework code. Transaction IDs on
offer-hub.org/stats
 verifiable on Stellar Expert. Disbursement receipts from minimum 3 corridors.
DELIVERABLE 3.4 — QA + Monitoring Stack + Production Resilience: Real-user QA with community members independent of the team. Production monitoring and alerting stack. Resilience engineering for the system to handle failures without manual intervention.
Cost:
 $10,000 (Testing & Verification + Core Development): QA environments ($2,400) + E2E automated suite ($2,500) + monitoring stack with minimum 3 alerts ($2,500) + resilience/retry logic ($1,600) + bug fixes ($1,000).
Verification:
 minimum 5 QA session recordings published on GitHub. Minimum 5 GitHub Discussions threads. E2E suite with execution results. Active monitoring dashboard with 3 functional alerts.
DELIVERABLE 3.5 — Open-Source Integration Adapters:
 3 independent npm packages under MIT license: (1) NestJS/TrustlessWork adapter, (2) SWK client-side signing module (4 Soroban operations), (3) BlindPay adapter (7 LATAM corridors). Each with README, executable examples, and tests.
Cost:
 $5,500 (Deployment & Release): packaging ~28 hrs ($3,000) + documentation ~28 hrs ($2,000) + third-party integration testing ~9 hrs ($500).
Verification:
 3 public GitHub repositories with confirmed MIT license. Published on npm. Verified executable examples per adapter.
TRANCHE #3 TOTAL:
 $30,500
HOW TO MEASURE COMPLETION:
✓
offer-hub.org
 live and functional on Stellar mainnet
✓ Validation framework published with results verifiable on Stellar Expert
✓ Minimum 3 fiat disbursements documented via BlindPay across LATAM markets
✓ E2E automated suite published with execution results
✓ QA session recordings published on GitHub
✓ Active monitoring dashboard with minimum 3 functional alerts
✓ 3 npm packages MIT license, README, executable examples
✓ Platform running on Stellar RPC — Horizon removed
Team
Two full-time engineers building on Stellar since 2024. As developers in Latin America, we built OfferHub because we have experienced firsthand how difficult it is to get paid reliably for international freelance work. That insight shapes every product and technical decision we make.
Kevin Brenes — Co-Founder & Blockchain Engineer
Full-stack and smart contract engineer with deployment experience across EVM, Starknet L2, Avalanche, Solana, and Stellar/Soroban. Previously shipped Galaxy DevKit (multi-chain dApp scaffolding framework) and SafeTrust (decentralized escrow dApp) both open source on GitHub. Placed in 5+ international hackathons across Starknet, Avalanche, and Solana. Leads all blockchain architecture, Soroban integration, and on-chain infrastructure at OfferHub.
LinkedIn:
https://www.linkedin.com/in/kevin-brenes-2a9750261/
GitHub:
https://github.com/KevinMB0220
Josué Araya Marín — Co-Founder & Full-Stack Engineer
Systems Engineering graduate specializing in scalable architecture for financial applications. Designed and built the complete OfferHub stack: NestJS orchestration API, Next.js frontend, Soroban escrow integration, PostgreSQL/Redis infrastructure, and Stellar RPC integration for real-time on-chain transaction monitoring. Leads product, architecture, and all frontend/backend systems.
LinkedIn:
https://www.linkedin.com/in/josue-araya-marin/
GitHub:
https://github.com/Josue19-08
WHY WE ARE THE RIGHT TEAM:
We have deployed and run a live Soroban escrow system with real users and verified on-chain transactions — not a prototype, not a demo.
Our LATAM background gives us direct market insight that no US or European team can replicate. We are not building for a market we researched we are building for a problem we live.
Two full-time founders with complementary skills: blockchain/on-chain (Kevin) and backend architecture/systems (Josué) — full technical coverage with no gaps.
COMBINED TRACK RECORD ON STELLAR:
103 users, 15 Soroban escrows created — verifiable at
offer-hub.org/stats
4 on-chain transactions confirmed on Stellar blockchain
103 Stellar wallets, 1:1 user/wallet ratio
190+ open-source contributors and 259 GitHub forks
Josué Araya Marín
Kevin Membreño Brenes
```
