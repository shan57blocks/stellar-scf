# Muney App: Stablecoin-to-Cash Layer Infrastructure

Source: https://communityfund.stellar.org/submissions/recnd9hYmEgcp2vB2 (SCF #44, awarded $95.0K, category: End-User Application)

- Website: https://muney.app
- Architecture doc: https://drive.google.com/file/d/1MswA_78S2yarWKlkW0336TOtoKgzOcGn/view?usp=sharing

## Links in the submission

- https://drive.google.com/file/d/1MswA_78S2yarWKlkW0336TOtoKgzOcGn/view?usp=sharing
- https://drive.google.com/file/d/1o_p4-f4M8hrt7fTAaayZz8hqn6mQ8u9-/view?usp=drive_link

## Submission text (as published)

```text
Products & Services
This submission adds a Stellar-powered cash-in/out product for El Salvador, built on top of Muney’s merchant network and B2B infrastructure.
User SDK
Muney will build a User SDK for wallets, fintechs, and money transmitters that want to offer cash-in/out in El Salvador without building their own merchant network. Through this SDK, partner apps can create cash-in/out requests, track transaction status, receive webhooks, and guide users to complete the cash transaction at an authorized Muney merchant.
Merchant SDK
Muney will build a Merchant SDK for its cash-in/out merchant network in El Salvador. Merchants will use it to confirm, process, and reconcile cash-in and cash-out transactions connected to USDC on Stellar. The merchant provides the last-mile cash liquidity, while Stellar powers the digital settlement layer.
USDC on Stellar Settlement
USDC on Stellar will be used as the settlement asset between partner wallets, Muney, and the merchant cash network. This allows users to move between Stablecoins and cash while partners keep using a simple SDK/API integration.
The initial product will launch with a 100-merchant pilot in El Salvador and is designed to scale toward 500 merchants.
Requested Budget
$95.0K
Traction Evidence
Current traction includes:
Approximately US$3.5M in cumulative transaction volume processed through Muney’s infrastructure. (
Link
)
Access to 12,000+ commerce locations / merchant touchpoints through Muney’s local cash-out and payout network.
Live operations and commercial development across 2 Latin American countries.
B2B/B2B2C model serving fintechs, remittance companies, payment partners, wallets, and businesses.
Compliance-aligned operations running on live volume, coordinated with regulated financial and payment partners where required by jurisdiction.
Active pipeline of B2B partners across fintech, remittances, payment processing, stablecoin wallets, and business payments.
Existing production experience converting Stablecoins value into local financial access
Tranche 1 (Deliverable Roadmap) - MVP
Tranche 1 — MVP / Stellar USDC Testnet Adapter - 20% / US$19,000
Purpose
Build the first working Stellar USDC settlement layer in testnet.
Deliverables
Stellar USDC adapter live on testnet.
Partner API endpoint supporting stellar_usdc as a settlement rail in sandbox.
Testnet transaction creation and detection.
Transaction state machine implemented.
Webhook events for transaction lifecycle.
Internal dashboard view for Stellar transactions.
Basic reconciliation export.
Idempotency and retry logic.
Recorded end-to-end demo.
Open-source adapter v0 published.
Demo Flow
Partner API request → Stellar testnet transaction → transaction confirmation → Muney payout orchestration status → reconciliation record → dashboard visibility.
Success Criteria
A partner can create a Stellar USDC payout request in sandbox.
Muney can track the Stellar transaction lifecycle.
Webhook events are generated correctly.
Reconciliation records are created.
The full flow is visible in the dashboard.
SCF reviewers can verify the testnet transaction.
Tranche 2 (Deliverable Roadmap) - Testnet
Tranche 2 — Anchor Platform + SDP Integration - 30% / US$28,500
Purpose
Integrate Stellar’s core payment infrastructure building blocks into Muney’s partner flows.
Deliverables
Anchor Platform integrated into Muney’s testnet flow.
Stellar Disbursement Platform integrated for bulk payout and remittance use cases.
Compliance event logging linked to Stellar transactions.
Sandbox available for pilot partner testing.
Automated tests for transaction state machine
Partner integration documentation published.
Open-source Anchor Platform example published.
Open-source SDP example published.
Reconciliation exports improved.
Pilot partner test flows completed.
Success Criteria
At least two pilot partners or partner simulations complete end-to-end testnet flows.
Stellar transaction evidence is verifiable.
Partner documentation is usable.
Compliance and reconciliation records are generated.
Anchor Platform and SDP flows are demonstrated.
You can find more information about development time for each integration partner here
Anchor Platform and SDP Budget Allocation Inside Tranche 2
Tranche 2 total:
US$28,500
Tranche 2
Component / Budget
Anchor Platform integration / US$10,500
Stellar Disbursement Platform integration / US$9,500
Compliance event logging and reconciliation mapping / US$2,500
Partner sandbox and pilot testnet flows / US$2,500
Automated tests and QA / US$2,000
Open-source examples and documentation / US$1,500
Total Tranche 2 / US$28,500
Anchor Platform Scope — US$10,500
Anchor Platform testnet deployment.
Stellar-compatible deposit and withdrawal workflows.
SEP-6 / SEP-24 / SEP-31 evaluation and implementation where applicable.
Integration with Muney partner approval and compliance workflows.
Transaction status mapping into Muney’s internal ledger.
Documentation of supported flows.
Stellar Disbursement Platform Scope — US$9,500
SDP testnet integration.
Bulk payout and disbursement flow configuration.
Recipient and payout status management.
Mapping SDP payout states into Muney’s orchestration engine.
Reconciliation records for partner and merchant flows.
Documentation and open-source example flow.
Tranche 3 (Deliverable Roadmap) - Mainnet
Tranche 3 — Mainnet Launch + Partner Pilot - 40% / US$38,000
Purpose
Launch USDC on Stellar as a production rail inside Muney and prove real-world use.
Deliverables
USDC on Stellar mainnet deployment.
Production partner API with Stellar rail enabled.
Mainnet monitoring and alerting.
Production reconciliation.
Live pilot with at least two B2B partner flows, which may include controlled production flows if one external partner timeline slips.
Run pilot with 100 merchants (list on
maps
)
Integration with 2 customers wallets or money transmitters
Real USDC on Stellar transaction evidence.
Dashboard views for mainnet Stellar transactions.
Final developer documentation.
Integration cookbook published.
Public demo page launched.
Initial post-launch metrics summary.
Success Criteria
Real USDC volume moves through Stellar using Muney infrastructure.
Local value is delivered through cash-in/out, bank payout, remittance payout, or B2B settlement.
SCF reviewers can verify mainnet transaction evidence.
Monitoring, reconciliation, documentation, and dashboard are production-ready.
At least two B2B partner flows demonstrate the rail in production or controlled production conditions.
Team
Muney is led by a three-founder team with experience across fintech, crypto, payments, merchant networks, product development, operations, partnerships, and emerging-market infrastructure.
The team has built and operated real-world products across Latin America and understands the complexity of moving from blockchain settlement to local payout execution.
Alberto Perdomo — Co-founder, Business, Strategy & Partnerships:
Alberto leads Muney’s business strategy, B2B partnerships, fundraising, ecosystem relationships, and market expansion. He is a full-stack founder and operator with experience across fintech, crypto, B2B sales, partnerships, distribution, and emerging-market operations. At Muney, he drives the institutional pipeline with fintechs, remittance companies, payment processors, financial institutions, and enterprise partners across Latin America.
Linkedin
X
Github
René Superamo — Co-founder, Tech & Infrastructure:
René leads Muney’s technical architecture, platform development, integrations, and product infrastructure. He owns the core technology stack behind Muney’s orchestration engine, partner API, settlement logic, dashboard, and the Stellar integration roadmap proposed in this Build Award.
Linkedin
Github
Eduardo Requena — Co-founder, Finance & Product:
Eduardo leads product operations, merchant network coordination, partner workflows, and operational execution. He is responsible for making Muney’s local payout and cash-out infrastructure work end-to-end, including merchant coordination, payout availability, operational processes, and transaction execution.
Linkedin
Extended Team:
Muney works with contributors and contractors across engineering, compliance operations, market expansion, partner support, and implementation.
For this SCF Build Award, Muney will allocate a dedicated integration team focused on Stellar development, Anchor Platform integration, SDP integration, API and dashboard updates, QA, testing, documentation, and partner pilot support.
Alberto
```
