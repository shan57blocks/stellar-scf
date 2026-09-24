# Azza: Scaling Azza Across Africa

Source: https://communityfund.stellar.org/submissions/rec2zu1jNgJIoO9K0 (SCF #44, awarded $89.0K, category: End-User Application)

- Website: https://www.useazza.com
- Architecture doc: https://ivory-tory-46.tiiny.site

## Links in the submission

- https://dune.com/devjosh/useazza-onchain-volume

## Submission text (as published)

```text
Products & Services
Azza has built stablecoin payment infrastructure for Africa, and this submission will support our planned Stellar integration to expand what the platform can do. First, we plan to use Stellar to power cross-border payments to the US, Europe, and Asia through Stellar’s partner bridge infrastructure, while also improving liquidity management across corridors. Second, we plan to use Stellar to offer yield to users through Blend, regardless of the chain they originally deposit from. These improvements will help us scale more efficiently across Africa, improve capital and liquidity efficiency, and expand the financial services we can offer users beyond payments.
Requested Budget
$89.0K
Traction Evidence
Traction Evidence
Azza is already live and being used for real stablecoin-powered payments across Africa. To date, we have processed over
$5M+ in fiat volume
,
$20M+ in on-chain volume
, completed more than
75,000+ transactions
, and served over
12,000+ users
. We currently support
12 fiat corridors
 and have built the custody, KYC, payout orchestration, and fiat settlement infrastructure needed to run these flows in production. Public on-chain activity can be seen on our Dune dashboard:
https://dune.com/devjosh/useazza-onchain-volume
.
Azza has also continued expanding into new markets and payment corridors. We have publicly launched support for additional markets including
Ghana
 (
https://x.com/useazza/status/1962500323206893606?s=20
),
Argentina
 (
https://x.com/ToochukwuOkoro2/status/1990936971632382060?s=20
), and
broader cross-border payment expansion through new supported rails (
https://x.com/useazza/status/2050228845924819275?s=20
).
Azza’s growth has also been validated externally. We have been featured by
TechCabal
 and recognized by different ecosystems and ecosytem leaders including Celo Blockchain (
Link)
, Damilare from Base (
Link1)
, Koko from ETHSafari (
Link2)
, SuperteamNG (
Link3)
, Solana name service (
Link4),
cNGN (
Link5),
Lisk Blockchain (
Link6)
, Base Africa (
Link7),
 Sophia from Ethereum Foundation (
Link8)
, XeustheGreat (
Link9)
,
 and Harri Obi (
Link10)
We were also the
only African startup selected for
Superteam Ignition S4
, participated in the
Lisk × CV VC Cohort 2025
, and were selected for
Magma 3.0 by Lava VC
.
Tranche 1 (Deliverable Roadmap) - MVP
TRANCHE 1 (DELIVERABLE ROADMAP) - STELLAR READINESS, MVP, AND TESTNET FOUNDATION
Goal:
 Establish Azza’s Stellar treasury and settlement base, complete Soroban / Blend v2 readiness on testnet, connect Bridge in sandbox, and stand up the full staging environment before product-facing rollout.
1. Stellar treasury account, trustline setup, and USDC settlement on testnet
Brief description:
Set up Azza’s Stellar treasury account, establish required USDC trustlines, and enable testnet USDC transfers with Horizon-based confirmation and reconciliation.
How to measure completion:
A testnet USDC transfer is successfully sent and received on Stellar, confirmed via Horizon, and reflected correctly in Azza’s internal ledger and reconciliation logs.
Budget:
$4,500
2. Soroban / Blend v2 integration service on testnet
Brief description:
Build and test the Soroban-based integration layer that deposits pooled USDC into Blend v2 and withdraws it back to Azza’s treasury account on testnet.
How to measure completion:
A full testnet cycle succeeds for:
contract simulation,
USDC deposit into Blend v2,
withdrawal back to Azza treasury,
and internal balance reconciliation.
Budget:
$6,000
3. Bridge sandbox integration and provider adapter
Brief description:
Connect Azza to Bridge’s sandbox / orchestration environment and implement the first version of the Bridge provider adapter for Stellar-settled international payouts.
How to measure completion:
A sandbox payout instruction is accepted, the corresponding Stellar-settled leg is observed, and the result is reconciled successfully in Azza’s payout system.
Budget:
$5,000
4. Staging environment and integration test harness
Brief description:
Stand up the staging environment for Stellar integrations, including:
Horizon listener,
Savings Service,
Bridge provider scaffolding,
database schema updates,
and monitoring hooks.
How to measure completion:
Savings and payout flows both execute end-to-end in staging using Stellar testnet and Bridge sandbox, with logs and reconciliation visible to the engineering team.
Budget:
$3,500
5. Project kickoff, readiness planning, and technical setup
Brief description:
Complete the initial project kickoff and engineering setup needed to begin delivery, including implementation planning, environment preparation, coordination, and early Stellar / Soroban readiness work before the core testnet build.
How to measure completion:
The engineering plan, working environments, technical ownership, and delivery setup are completed, enabling the full Stellar integration workstream to proceed on schedule.
Budget:
$9,500
Tranche 1 Total: $28,500
Tranche 2 (Deliverable Roadmap) - Testnet
TRANCHE 2 (DELIVERABLE ROADMAP) - CORE PRODUCT FLOWS END-TO-END
Goal:
 Integrate Stellar-powered savings and payout flows into the Azza product, while adding accounting, controls, and payout routing for controlled beta/testnet operation.
1. WhatsApp savings deposit + balance / yield display
Brief description:
Enable users to initiate Azza Savings deposits from WhatsApp and view their savings balance and current APY.
How to measure completion:
A savings deposit flow works in the product environment, and the displayed balance and APY update correctly from tracked backend state.
Budget:
$6,000
2. Savings withdrawal to USDC and local fiat
Brief description:
Support full savings withdrawals back into user USDC balance or through Azza’s existing local fiat offramp system.
How to measure completion:
A complete withdrawal flow succeeds from savings balance into either USDC or local fiat settlement, with correct internal accounting and reconciliation.
Budget:
$5,000
3. Bridge provider live in Azza’s Provider Router for USD / EUR / GBP payouts
Brief description:
Add Bridge as a payout provider inside Azza’s routing system for international corridors.
How to measure completion:
The router correctly selects Bridge for USD, EUR, or GBP payout scenarios, and those payouts settle via Stellar and deliver through the configured payout flow in staging/beta.
Budget:
$7,000
4. International onramp via Bridge: USD / EUR / GBP funding into USDC
Brief description:
Enable inbound international funding through Bridge that converts into USDC inside Azza.
How to measure completion:
USD, EUR, or GBP funding is converted into USDC and reflected in Azza’s balance and reconciliation system.
Budget:
$3,500
5. Yield Accounting Service
Brief description:
Implement the accounting logic that tracks each user’s savings share, accrued yield, and the mapping between internal user balances and Blend v2 positions.
How to measure completion:
Per-user savings shares and APY calculations pass tests and reconcile correctly against expected Blend v2 accrual behavior.
Budget:
$4,500
6. Risk monitoring and reconciliation
Brief description:
Add protocol guardrails, reconciliation logic, and Bridge webhook verification, including alerting on mismatches or threshold breaches.
How to measure completion:
Alerts fire correctly in staging/beta when utilisation thresholds are breached or when webhook / settlement mismatches occur.
Budget:
$2,500
Tranche 2 Total: $28,500
Tranche 3 (Deliverable Roadmap) - Mainnet
TRANCHE 3 (DELIVERABLE ROADMAP) - MAINNET LAUNCH, ADOPTION, AND PRODUCTION READINESS
Goal:
 Launch both integrations on mainnet, onboard a controlled beta cohort, measure Stellar ecosystem outcomes, and complete operational hardening.
1. Blend v2 savings live on mainnet
Brief description:
Launch Azza Savings in production, enabling live user USDC balances to be supplied into Blend v2 through Azza’s custody and accounting layer.
How to measure completion:
Live user savings deposits are executed successfully on Stellar mainnet, reflected in Azza’s product experience, and tracked in the reconciliation layer.
Budget:
$8,000
2. Bridge payout rails live on mainnet for USD / EUR / GBP corridors
Brief description:
Launch Bridge-powered international payout rails in production.
How to measure completion:
A live international payout completes successfully through the Bridge integration, with the Stellar settlement leg and final delivery state both recorded and reconciled.
Budget:
$8,500
3. Full QA across both integrations
Brief description:
Complete end-to-end QA, edge-case testing, failure injection, and reconciliation testing for both savings and payout flows.
How to measure completion:
A QA report is completed showing all major failure paths tested, with no silent fund-loss scenarios across savings or payout flows.
Budget:
$7,000
4. Production observability
Brief description:
Deploy dashboards, alerts, and anomaly detection for both the savings and payout systems.
How to measure completion:
Monitoring is live in production and alerts trigger correctly for all defined failure scenarios.
Budget:
$5,000
5. Documentation, SCF reporting, and handover
Brief description:
Finalize architecture documentation, runbooks, reconciliation procedures, rollout learnings, and SCF reporting materials.
How to measure completion:
Final architecture docs, runbooks, reporting materials, and operational handover assets are completed and ready for ongoing use.
Budget:
$3,500
Tranche 3 Total: $38,000
Team
Azza is an 8-person team led by 3 co-founders, building stablecoin payment infrastructure for Africa.
Toochukwu Okoro (CEO | Blockchain Engineer & Founder)
Toochukwu is a blockchain engineer and founder building Azza, a stablecoin payments platform focused on making finance in Africa borderless. He leads product, infrastructure, and ecosystem strategy, with hands-on experience building payment rails, onramp and offramp systems, wallet infrastructure, settlement orchestration, and cross-border stablecoin flows. His work at Azza spans product architecture, partnerships, market expansion, and operational execution across multiple African payment corridors.
LinkedIn:
https://www.linkedin.com/in/toochukwu-okoro/
GitHub:
https://github.com/KingzRex
X:
https://x.com/ToochukwuOkoro2
Joshua Avoaja (CTO | Full-Stack / Backend Engineer)
Joshua Avoaja is a product-minded builder and operator with a strong track record across startup execution and hackathon-driven innovation. He led his team to a runners-up finish at the global Encode x Polygon Hackathon with BlocTix, an NFT ticketing platform, won first place at the Lisk SDK Hackathon for Pollify, a decentralized voting application, and placed second at the Neo X Grind Hackathon with Onchain Buddy, an AI-powered WhatsApp bot for simplifying blockchain interactions. He was also accepted into Antler Nairobi, one of Africa’s most selective early-stage founder programs.
LinkedIn:
https://www.linkedin.com/in/dev-josh/
GitHub:
https://github.com/joshDamian
X:
https://x.com/DevJosh__
Eluke Victor (Operations / Product Lead)
Victor Eluke is an operations and product leader with experience across fintech, blockchain, and growth systems. As Co-Founder and COO of Azza, he has helped build and scale a WhatsApp-based stablecoin wallet for crypto payments and cross-border transactions, leading work across product strategy, operations, user research, customer experience, partnerships, and growth. He has contributed to scaling Azza to over 12,000 users, building a community of more than 6,000 members, and supporting operational systems that facilitated over $20 million in trading volume. His work has also helped reduce onboarding friction and significantly improve customer support and operational efficiency. Before Azza, he co-founded Blocverse, where he worked with startups building blockchain and fintech products. He also founded the Blockchain Mata Campus Club at Alex Ekwueme Federal University and has been recognized through multiple regional and international innovation programs and hackathons.
LinkedIn:
https://www.linkedin.com/in/victor-eluke-b065bb1a1/
X:
https://x.com/ElukeV
Toochukwu Okoro
```
