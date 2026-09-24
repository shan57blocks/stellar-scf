Source: https://drive.google.com/file/d/1dNxRmPmUC3fR1VZghoqyqJUW2X4WDniZ/view ("ElementPay Stellar Technical Architecture.docx" in the submission's evidence folder https://drive.google.com/drive/folders/1IoyP5JofRPQZ-8lEk6034BqVs1HyoLz1). The architecture link in the submission itself (https://docs.google.com/document/d/1CvOux9kW4VyKa84TgohxSKK_grFUPNvK/) no longer opens (404/410).

ElementPay Stellar Integration

Technical Architecture — SCF Build Integration Track
1. Executive Summary
ElementPay is building stablecoin payment infrastructure for Africa and the Global South. The platform helps businesses use stablecoins for invoicing, collections, payouts, local settlement, liquidity operations, and reconciliation through enterprise APIs and a dashboard.
This document describes ElementPay's Stellar integration for the SCF Build Integration Track. The integration is Stellar-specific and focuses only on building blocks listed in the SCF Integration List, plus a Soroban port of ElementPay's production escrow contract so Stellar orders get the same on-chain lifecycle EVM orders already have. The goal is Stellar-supported invoicing, collections, payouts, transaction monitoring, liquidity visibility, cross-chain USDC support, and reconciliation inside ElementPay. A companion Backend Infrastructure Plan covers the engineering design in depth, including implementation diagrams; this document carries scope, deliverables, timeline, and the itemized three-tranche budget.
2. Selected Stellar Integration List Building Blocks
Building block
How ElementPay will use it
Role in product
Stellar Wallets Kit
Wallet connection and user authentication for Stellar-compatible wallets
Foundation for wallet access and Stellar account interaction
Stellar Disbursement Platform
Bulk payout and business disbursement flows for enterprise customers
High-volume payouts, status tracking, payout reconciliation
Aquarius
Liquidity visibility and swap routing insights for Stellar-based settlement flows
Internal liquidity and settlement decisioning
CCTP
Native USDC movement between Stellar and supported external chains
Cross-chain USDC settlement flexibility and liquidity movement

Anchor Platform is not included in this scope; ElementPay may evaluate anchor infrastructure in a future phase. This submission covers Wallets Kit, SDP, Aquarius, CCTP, and the Soroban escrow port described in Section 3.3.
3. Current ElementPay Architecture
	•	Business dashboard for payment visibility and operations (Next.js/React).
	•	Enterprise APIs for invoices, collections, payouts, transaction tracking, and reconciliation.
	•	Stablecoin invoicing and collections workflows.
	•	On/off-ramp infrastructure connected to local payment rails (mobile money, bank settlement).
	•	Multi-chain liquidity operations across EVM chains.
	•	A working on-chain event listener: multi-chain event monitoring, webhook signing, backfill, health checks, and Docker deployment, already public in the org's GitHub history.
	•	Internal ledger references for invoices, payouts, customers, settlement records, and transaction status.
	•	Webhook infrastructure for payment state changes and operational updates.
The Stellar integration extends this infrastructure rather than replacing it. Stellar transaction data is mapped into ElementPay's existing ledger, dashboard, APIs, and reconciliation system.
3.1 Backend Infrastructure Plan (expanded per panel request)
ElementPay's current on-chain settlement layer is built around an EVM-specific service (Web3Manager), which wraps web3.py/AsyncWeb3 and resolves connections by ERC-20 token contract address. The platform's order schema is correspondingly EVM-native today: order objects reference an ERC-20 token contract and an EVM wallet_address.
Stellar does not fit into this shape directly. It has no ERC-20 contracts, uses ed25519 G... public keys rather than 0x addresses, represents assets as issuer+code pairs, and requires recipients to establish a trustline before they can hold a non-native asset like USDC. The integration plan is therefore:
	•	A new, parallel settlement module for Stellar (StellarManager), built alongside Web3Manager rather than forced into it, mirroring the same clean adapter pattern ElementPay already uses on the fiat PSP side (a BasePSP interface every provider implements identically). Stellar becomes a peer service, not a special case bolted onto EVM-specific code.
	•	A chain-aware order schema extension: the API accepts an explicit chain discriminator so Stellar-denominated orders carry Stellar-shaped references (asset issuer+code, G... address) without breaking existing EVM integrations built against the current schema.
	•	Trustline handling as an explicit pre-condition check before any Stellar USDC transfer is attempted, surfaced clearly to the business or customer rather than failing silently on-chain.
	•	Reuse of the existing event-listener infrastructure (webhook signing, backfill, health checks, Docker deployment) as the operational pattern for the new Stellar transaction monitor.
The companion Backend Infrastructure Plan specifies every service, table, worker, and state machine behind these four points, with implementation diagrams.
3.2 Development Transparency
To address the panel's note on commit cadence: ElementPay will maintain a dedicated stellar-integration branch and changelog with Stellar-specific commits published on a weekly cadence starting at T1 kickoff, distinct from the org's general repository activity, so integration progress is independently verifiable throughout the grant period rather than only at tranche delivery.
3.3 Smart Contract Parity: Soroban Port of the Escrow Contract (Tranche 1 Scope)
ElementPay's existing EVM order lifecycle is enforced on-chain by an upgradeable escrow contract, OrderManagement.sol (UUPS proxy, OpenZeppelin), deployed today on Base mainnet and Base Sepolia. It implements a compact Pending to Completed or Cancelled state machine with three state-changing entry points, createOrder, settleOrder, and refundOrder, gated by an aggregator-only access modifier, moving an ERC-20 token between user, contract, and treasury.
ElementPay will port this contract to Soroban (Stellar's Rust/WASM smart contract platform) within Tranche 1 and deploy it to Stellar testnet, so Stellar orders get the same on-chain escrow lifecycle EVM orders already have, rather than a different settlement model per chain. Every piece of the Solidity contract has a direct Soroban counterpart:
OrderManagement.sol (Solidity/EVM)
Soroban equivalent
mapping(bytes32 => Order)
Persistent contract storage keyed by order id
ERC-20 transferFrom / transfer
SEP-41 token client calls against the Stellar Asset Contract wrapping USDC
onlyAggregator modifier
Soroban require_auth() on the aggregator address
UUPS upgradeability
Soroban's native contract-Wasm-upgrade mechanism
Pausable
Manual paused flag and guard (no built-in Soroban equivalent)
keccak256(...) order-id generation
Soroban crypto host functions (e.g. sha256) or a ledger-sequence-based counter

This is new surface area for the team, since no Rust/Soroban code is in ElementPay's stack today, so it is scoped conservatively for T1: testnet deployment and integration only. Any contract holding custody of user funds needs a security audit before it can safely hold real money; consistent with Section 8, that audit is expected as part of the final-tranche process using SCF audit credits, so mainnet deployment of the Soroban contract is gated on that audit completing in Tranche 3, not promised earlier. The monitored-transfer model (Sections 3.1 and 4) remains available as the fallback settlement path if contract-based settlement is not yet audit-cleared by mainnet cutover.
4. Target Stellar Architecture
	•	Business creates an invoice, collection, or payout request in ElementPay.
	•	ElementPay determines whether the transaction should use a Stellar-supported flow.
	•	ElementPay creates or connects the required Stellar account or wallet session.
	•	Stablecoin payment or payout is initiated through Stellar-supported infrastructure.
	•	ElementPay monitors the transaction and records Stellar transaction identifiers.
	•	Transaction status is synced to ElementPay APIs, webhooks, and dashboard.
	•	Where relevant, payout or settlement records connect to local payment operations.
	•	Business users reconcile Stellar transactions against invoices, payouts, and settlement records.
Layer
Component
Responsibility
User and business access
ElementPay dashboard, APIs, Stellar Wallets Kit
Wallet connection, payment request creation, Stellar activity visibility
Payment orchestration
ElementPay backend services
Invoices, collections, payouts, references, status objects, webhook events
Stellar transaction layer
StellarManager, Stellar SDK, Soroban escrow contract, transaction monitoring
Execute and monitor Stellar-based stablecoin transfers and escrow contract calls, including trustline checks
Bulk payouts
Stellar Disbursement Platform
Enterprise disbursement, payout status tracking
Liquidity visibility
Aquarius
Stellar liquidity and swap routing data
Cross-chain USDC movement
CCTP
Native USDC movement between Stellar and supported chains
Reconciliation
ElementPay ledger and dashboard
Map Stellar transaction records to invoices, payouts, settlement records, customer references

5. Integration Details by Building Block
5.1 Stellar Wallets Kit
Wallet connection and authentication flow inside ElementPay; backend session handling; wallet address capture mapped to ElementPay user and business profiles; transaction signing flow where user approval is required, including signing the Soroban create_order deposit in the contract-settled path.
5.2 Stellar Disbursement Platform
Business payouts and bulk disbursements for payroll, supplier payouts, creator payouts, marketplace disbursements, and remittance-related settlement. Payout batch creation, status and exception tracking, webhook updates, and mapping to ElementPay payout IDs, business IDs, recipient references, and settlement states. SDP is self-hosted (its own services and PostgreSQL), which is why it anchors the final tranche.
5.3 Aquarius
Liquidity and routing data for Stellar-native settlement activity, surfaced internally for treasury and settlement decisioning, persisted on a defined refresh interval so history, not just point-in-time reads, informs decisions.
5.4 CCTP
Native USDC movement between Stellar and supported external chains through the burn, attestation, and mint flow; source and destination chain tracking; mapping to ElementPay settlement records for end-to-end reconciliation, with a treasury view of in-flight transfers.
6. Data Model and Reconciliation Mapping
ElementPay object
Stellar / integration reference
Purpose
Invoice ID
Stellar transaction hash or payment reference
Links incoming stablecoin payment to a business invoice
Collection ID
Stellar account, asset, transaction hash
Tracks customer or counterparty collections
Payout ID
SDP payout reference and Stellar transaction hash
Tracks business disbursements to recipients
Customer reference
Wallet address or recipient reference
Links payment activity to a business customer or recipient
Settlement record
Stellar transaction status, contract order state, CCTP status, liquidity data
Tracks settlement state, liquidity movement, reconciliation output
Webhook event
Transaction status update
Notifies business systems of payment lifecycle changes

7. API and Webhook Changes
	•	Create Stellar-supported invoice, collection, and payout or payout batch.
	•	Fetch transaction status and reconciliation record.
	•	Fetch Stellar transaction details mapped to ElementPay references, including contract order state.
	•	Webhook events for pending, completed, failed, refunded, and settled states.
Example webhook fields: elementpay_transaction_id, invoice_id or payout_id, stellar_transaction_hash, asset_code, amount, status, business_reference, settlement_reference, created_at, updated_at.
8. Security, Reliability, and Compliance Considerations
	•	Separation of user-facing wallet connection flows from backend operational wallets.
	•	Environment separation for development, staging, testnet, and production.
	•	Secure management of API keys and credentials; per-operation signing keys.
	•	Webhook signature verification.
	•	Transaction retry and failure handling for payouts and settlement events.
	•	Manual review controls for failed, flagged, or high-value transactions.
	•	Internal audit and operational logging.
	•	KYB and compliance workflow integration for enterprise customers where required.
SCF security audit credits are expected as part of the final tranche process; audit costs are excluded from this budget, and mainnet deployment of the Soroban escrow contract is gated on that audit (Section 3.3).
9. Deliverables, Measurement, and Budget (Itemized)
Total requested: $69,999 worth of XLM, split across exactly three deliverable tranches. There is no separate approval line and no contingency line: architecture finalization and environment setup are absorbed into Tranche 1 (which is the largest tranche for that reason), and SDP deployment risk is priced inside the Tranche 3 SDP line rather than held in a reserve. 
Tranche 1 — Foundation Layer and Soroban Escrow — $31,200
Target: 4 weeks from approval.
Item
Cost
Architecture finalization and environment setup (dev/staging/testnet/production separation)
$2,400
Stellar Wallets Kit integration (connect flow, session handling, address capture)
$600
Stellar account support in backend (StellarManager, asset registry, trustline checks)
$4,800
Soroban escrow contract port (createOrder/settleOrder/refundOrder, storage, auth — §3.3)
$3,600
Soroban contract test suite (lifecycle, adversarial, and custody-invariant tests)
$1,800
Soroban testnet deployment and StellarManager integration wiring
$1,200
Transaction monitoring (Horizon stream + Soroban event ingestion, extends existing event-listener infra)
$4,800
Internal reference mapping (invoice/collection/payout/customer/settlement)
$3,600
Dashboard visibility for Stellar transactions
$2,400
Testnet QA
$1,800
KYB/compliance workflow integration for enterprise customers (per §8)
$2,400
Cross-tranche program coordination (T1 share)
$1,800

Completion evidence: live Wallets Kit connect flow on testnet; a StellarManager service passing integration tests; the Soroban escrow contract deployed to Stellar testnet with createOrder/settleOrder/refundOrder exercised end-to-end and passing its test suite; at least 10 real testnet transaction hashes recorded against ElementPay invoice/payout references; dashboard screen showing Stellar transaction activity linked to those references; KYB workflow gating Stellar-supported flows for enterprise customers; weekly public commits on the dedicated stellar-integration branch from kickoff.
Tranche 2 — CCTP and Aquarius — $12,000
Target: 3 weeks after T1 (7 weeks post-approval).
Item
Cost
CCTP core integration (burn, attestation polling, mint pipeline)
$2,400
CCTP settlement mapping/reconciliation
$2,400
Treasury in-flight transfer visibility for cross-chain USDC
$1,200
Aquarius integration (liquidity/routing data fetch)
$600
Liquidity dashboard/reporting (snapshot history on a defined refresh interval)
$1,800
Failure-mode and load testing (dropped transfers, retry/timeout handling, cross-chain edge cases)
$2,400
Cross-tranche program coordination (T2 share)
$1,200

Completion evidence: at least 2 real CCTP transfer records (source/destination chain, transaction hash) reconciled against ElementPay settlement records; Aquarius liquidity data visible in an internal dashboard view, refreshed on a defined interval; documented failure-mode test results for dropped and retried transfers.
Sequencing note: CCTP and Aquarius are the lighter integrations (per panel-supplied estimates: a few days and under a day respectively) and are scheduled between the foundation and SDP tranches, so the team has a working transaction-monitoring and reconciliation pattern proven in T1 before taking on SDP's heavier self-hosted-deployment lift in T3.
Tranche 3 — SDP, Pilot, and Launch — $26,799
Target: 3 weeks after T2 (10 weeks post-approval).
Item
Cost
SDP core integration (self-hosted multi-service deployment with dedicated PostgreSQL + API integration; deployment risk priced in)
$6,600
Payout backend (batch creation, status tracking, exception handling)
$3,600
Webhook events for payout status
$1,800
Payout reconciliation records
$1,800
End-to-end testing across the full flow
$2,400
Pilot business onboarding (2 named businesses)
$2,400
Pilot cohort expansion (2 additional named businesses, total 4, with dedicated onboarding support)
$2,400
Technical documentation
$1,800
Production hardening (env separation, logging, security prep, mainnet cutover)
$2,400
Soroban contract security-review preparation and SCF audit-credit coordination
$999
Cross-tranche program coordination (T3 share)
$600

Completion evidence: at least 3 real SDP payout batches executed with recorded batch IDs and per-recipient status; webhook delivery logs for payout state changes; 4 named pilot businesses (up from the earlier minimum of 2) with completed real transactions and dashboard-visible reconciliation; published technical documentation; production/mainnet-equivalent deployment live; Soroban contract audit-review process initiated under SCF audit credits.
Budget Summary
Tranche
Target completion
Budget
Tranche 1 — Foundation Layer and Soroban Escrow
4 weeks post-approval
$31,200
Tranche 2 — CCTP and Aquarius
7 weeks post-approval
$12,000
Tranche 3 — SDP, Pilot, and Launch
10 weeks post-approval
$26,799
Total

$69,999

Tranche 1 carries the largest share deliberately: it absorbs architecture and environment setup, and it front-loads the two highest-skill items in the program, the parallel Stellar settlement service and the Soroban contract port, so the riskiest engineering is de-risked earliest and every later tranche builds on proven infrastructure.
10. Implementation Timeline
Windows are relative to the actual approval date; the original fixed calendar dates predate implementation start and had lapsed by panel review.
Window
Workstream
Expected output
Weeks 1–4 (post-approval)
Wallets Kit, StellarManager, Soroban escrow contract port and testnet deployment, transaction monitoring, reference mapping, dashboard visibility
Foundation layer live on testnet, weekly public commits
Weeks 5–7
CCTP integration and settlement reconciliation, Aquarius liquidity visibility
Cross-chain USDC movement and liquidity visibility live
Weeks 8–10
SDP payout workflows and webhooks, end-to-end testing, named pilot transactions, documentation, production readiness
Mainnet or production-equivalent launch

11. Mainnet Launch Criteria (tightened per panel feedback)
Launch is complete when all of the following concrete artifacts exist, not when flows are subjectively "working":
	•	At least 10 real Stellar transaction hashes tied to ElementPay invoice/collection/payout references (testnet during T1, mainnet by T3).
	•	Soroban escrow contract deployed and exercised on testnet during T1 (createOrder/settleOrder/refundOrder); mainnet deployment gated on SCF audit-credit review completing in T3.
	•	At least 3 SDP payout batch IDs with recorded per-recipient status.
	•	At least 2 CCTP transfer records with source/destination chain and transaction hash, reconciled against ElementPay settlement records.
	•	Webhook delivery logs covering pending, completed, failed, refunded, and settled events.
	•	Reconciliation exports linking Stellar activity to ElementPay invoices, payouts, and settlement records.
	•	At least 2 named pilot businesses with completed real transactions, confirmed in writing (4 onboarded in total).
	•	Published technical documentation, internally reviewed.
	•	Production or mainnet-equivalent environment live and accessible.
12. Expected Impact on Stellar Ecosystem
This integration brings ElementPay's payment infrastructure and African business use cases into the Stellar ecosystem: real-world stablecoin payments, business payouts, invoicing, collections, local settlement, and cross-border commerce in Kenya and Ghana. ElementPay's API and reconciliation layer make Stellar-based stablecoin payments usable in real commercial workflows, and the Soroban escrow port demonstrates that a production EVM payments contract translates cleanly onto Stellar's contract platform, a pattern other multi-chain payment teams can follow.
13. Appendix: Product Scope
In scope: Stellar-supported stablecoin invoicing, collections, business payouts and bulk disbursements, a Soroban port of ElementPay's EVM escrow contract deployed to testnet in Tranche 1 (Section 3.3), transaction monitoring and dashboard visibility, reconciliation mapping, Aquarius liquidity visibility, CCTP-supported USDC movement, and a mainnet or production-equivalent launch.
Out of scope: marketing and promotional spend; security audit costs (handled via SCF audit credits); mainnet deployment of the Soroban escrow contract ahead of audit completion (Sections 3.3 and 11); Anchor Platform production implementation (future phase); non-Stellar integrations outside the SCF Integration List.
