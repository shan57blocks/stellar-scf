Source: https://drive.google.com/file/d/18-EwvzaVWZ7sm4sQNnSjlFK10Jk-C5gP/view ("ElementPay Stellar Backend Infrastructure Plan.docx" in the submission's evidence folder). Converted from .docx to text; diagrams (images) are not included.

ElementPay Stellar Integration

Backend Infrastructure Plan
Companion to the Technical Architecture document. Budget and tranche pricing live there; this document is the engineering record that stands behind them.
1. Purpose
This plan documents the backend engineering behind the Stellar integration: ElementPay's current production stack, the reasons that stack cannot absorb Stellar unmodified, and every new service, contract, table, worker, state machine, and operational control the program builds, with implementation diagrams throughout. Every component maps either to a module in the existing codebase (the Element Aggregator) or to a new module built in this program.
2. Current Backend Stack (Production Today)
The Element Aggregator is the orchestration layer of the platform: it routes every order to the best payout partner, whether a liquidity provider or a PSP, bridging fiat payment rails and blockchain settlement.
Layer
Technology
Role
API framework
Async Python 3.11 service
Serves order, webhook, KYC, admin, and partner APIs; async request handling throughout
Database
MySQL 8.x, SQLAlchemy 2.0 (async ORM), Alembic
Orders, wallets, users, PSP configuration, escrow, API keys, KYC links; versioned schema migrations
Blockchain settlement (EVM)
web3.py / AsyncWeb3, pooled HTTP providers per chain
Web3Manager maintains one connection pool per EVM chain (Base, Lisk, Scroll, Arbitrum, Polygon, BSC), routes by ERC-20 token address
On-chain order lifecycle
OrderManagement.sol escrow (UUPS proxy, OpenZeppelin) on Base mainnet and Base Sepolia
createOrder emits OrderCreated, backend triggers fiat leg, backend calls settleOrder / refundOrder, contract emits OrderSettled / OrderRefunded
Fiat PSP layer
BasePSP abstract interface + DB-driven routing engine
One standard interface implemented per provider (M-Pesa, SasaPay, Cellulant, others); PaymentRail / PSPConfiguration tables drive priority-based routing per currency and amount
Background workers
asyncio tasks under the service lifecycle
Hot-wallet balance monitor, FX rate updater, partner-order reconciliation loop, DB health checker
Event listener
Multi-chain event monitoring, public in the org's GitHub history
Webhook signing, backfill, health checks, Docker deployment
Security
Per-operation signing keys, Fernet payload encryption, HMAC webhook signatures
Separate keys for creation / settlement / refund; encrypted order metadata; signed outbound webhooks
Deployment
Docker image behind Caddy reverse proxy; environment-driven config
Dev / staging / testnet / production separated at the config level

3. Why the EVM Layer Cannot Absorb Stellar As-Is
	•	Addressing and keys: EVM uses secp256k1 keys and 0x addresses; Stellar uses ed25519 keys, G-prefixed accounts, M-prefixed muxed addresses, and C-prefixed contract addresses. A parallel signing and validation path has to be built.
	•	Asset model: EVM assets are ERC-20 contracts resolved through CONTRACT_ADDRESS_MAP. Stellar assets are issuer+code pairs, exposed to Soroban through Stellar Asset Contracts implementing the SEP-41 token interface. The same code (USDC) can exist under multiple issuers, so issuer verification is a safety requirement, not a lookup.
	•	Trustlines and reserves: A Stellar account cannot hold USDC until it establishes a trustline to the issuer, and every trustline locks base-reserve XLM. The backend must provision trustlines, keep accounts above reserve floors, and pre-validate recipient trustlines before payouts.
	•	Sequence numbers and throughput: Each account submits one transaction per ledger (about 5 seconds) without coordination; high throughput needs explicit sequence management and a channel-account pattern. EVM nonce handling does not transfer.
	•	Contract platform: Soroban contracts are Rust compiled to WASM, fees are resource-metered rather than gas-priced, contract state is subject to archival and needs TTL extension, and every invocation is simulate-then-assemble-then-submit against RPC. Porting the escrow means re-designing it for this model, not transpiling it.
Stellar support is therefore a parallel settlement rail alongside the EVM layer, with a dedicated Soroban contract workstream. Nothing in the EVM path is rewritten.

Figure 1. Target backend architecture: the Stellar rail as a peer of the EVM rail inside the Element Aggregator.
4. Stellar Settlement Service
4.1 StellarManager
	•	One pooled async client per configured network for both Horizon and Soroban RPC (testnet and public), mirroring how Web3Manager pools RPC providers per chain.
	•	An asset registry of issuer+code pairs per network, including each asset's Stellar Asset Contract address, filling the role CONTRACT_ADDRESS_MAP plays for EVM; right-code wrong-issuer assets are rejected at the boundary.
	•	Classic transaction pipeline: build (operations, memo, time bounds), fee selection, sign, submit, confirm; time bounds make stuck transactions expire deterministically.
	•	Soroban invocation pipeline: simulate to obtain the authorization tree and resource footprint, assemble, sign, submit, poll to a terminal status, with a simulation-diff guard that blocks submission when simulated state does not match the expected order state.
	•	Fee strategy: base fee with a ceiling, fee-stats poller for surge detection, fee-bump resubmission for stranded transactions; Soroban resource fees come from simulation with a bounded margin, never hardcoded.
	•	Sequence management with tx_bad_seq recovery and a channel-account pool so concurrent submissions never serialize on one account.
4.2 Accounts, trustlines, and reserves
Account role
Purpose
Controls
Receiving accounts
Destination for Phase 1 invoice and collection payments; watched by the stream listener
Trustlines to supported assets; memo required; scheduled sweeps
Operations / hot account
Refunds, direct transfers, contract admin calls
Per-operation keys; balance-floor alerts via the existing hot-wallet monitor pattern
Channel accounts
Sequence fan-out for concurrent payout and contract-call submission
XLM only; created and rotated by a provisioning job
Aggregator identity
Authorized caller for settleOrder / refundOrder on the escrow contract
Dedicated key, used for nothing else, audit-logged

A trustline provisioning job runs at startup and on configuration change: verifies trustlines against the asset registry, establishes missing ones, and keeps XLM balances above minimum reserves (reserves scale with subentries). Recipient accounts are pre-validated for trustlines before payouts, so failures are caught before funds move.
4.3 Settlement model: two phases to lifecycle parity
	•	Phase 1, monitored transfers: Payments move directly from the customer's account to an ElementPay receiving account, carrying the order reference in the transaction memo. Ships first: smallest trusted surface, no audit needed to go live, and it exercises the full monitoring, reconciliation, and webhook pipeline on real transactions.
	•	Phase 2, Soroban escrow: The Soroban port of OrderManagement.sol (Section 5) brings the Stellar rail to lifecycle parity with the EVM path: contract custody and the same OrderCreated / OrderSettled / OrderRefunded event model the backend already consumes on EVM. Monitored transfers remain the fallback rail and serve wallets that cannot invoke contracts.

Figure 2. Stellar-funded order lifecycle across both settlement phases, converging on the unchanged fiat leg and webhook layer.
4.4 Memo-based reference scheme (Phase 1)
	•	Stellar text memos are limited to 28 bytes, so each order gets a short collision-checked payment reference at creation, surfaced through dashboard, API, and Wallets Kit.
	•	The listener matches incoming payments by exact memo against open orders on that account, plus asset and amount checks.
	•	Missing, malformed, or unknown memos are never guessed: they land in a manual-review queue with operator resolve and refund actions.
	•	Underpayments hold the order as partially_paid with a top-up window; overpayments settle and flag the excess for refund. In Phase 2 the contract takes the order id as an argument, eliminating this class of errors for contract-settled orders.
4.5 Data model changes
Table / field
Change
Why
wallets.chain
New value "stellar" alongside "evm"
Generic string column; no migration needed
wallets.address
Stores Stellar G... public key
ed25519 validation added at the API boundary
orders
New crypto_chain rail discriminator via Alembic migration
Dashboard, monitoring, and reconciliation branch on settlement chain
orders.*_transaction_hash
Unchanged; Stellar hashes stored as-is
Already chain-agnostic strings

New table
Contents
Used by
stellar_accounts
Public key, role, network, trustline state, reserve floor
Provisioning job, listener, payouts
stellar_stream_cursors
Monitored account or contract, last paging token or event cursor
Listener and ingester resume plus backfill
stellar_review_queue
Unmatched or mismatched payments, resolution state, operator actions
Manual review workflow
soroban_contracts
Contract address, WASM hash, network, admin identity, TTL watermark
Deployment pipeline, TTL keeper, invocation guard
disbursement_batches / _items
SDP batch reference, per-recipient status, payout IDs
SDP pipeline (Section 6)
cctp_transfers
Source/destination chain, burn tx, attestation, mint tx, state
CCTP state machine (Section 7)
liquidity_snapshots
Pair, venue, depth and routing data, captured_at
Aquarius visibility (Section 8)

4.6 Transaction monitoring
	•	Horizon payments stream (Phase 1): Streams payments per receiving account; every event's paging token is persisted after processing so a restarted listener resumes exactly where it stopped, with a backfill pass on start, mirroring the backfill and health-check pattern already built for the EVM listener.
	•	Soroban event ingester (Phase 2): Polls escrow contract events from Soroban RPC with a persisted cursor; because RPC retains a bounded event window, cursor freshness is alerted and a ledger-metadata backfill procedure covers outages longer than retention.
	•	Shared pipeline: Both paths converge on validation (asset registry, reference resolution, amount) and the same OrderRepo update path the EVM listener uses, so webhooks and dashboard state are identical regardless of rail.
	•	Idempotency: Reuses the status-check-before-update guard from EVM processing, so replays and backfills cannot double-process.

Figure 3. Dual ingestion, one pipeline: Horizon stream and Soroban events join the same validation, order update, and webhook path as the EVM listener.
4.7 Settlement state machine
State
Entered when
Exits to
created / awaiting_payment
Order created on the Stellar rail; reference issued or contract order created
payment_detected, expired
payment_detected
Memo match or OrderCreated event
confirmed, partially_paid, manual_review
partially_paid
Underpayment within tolerance (Phase 1 only)
confirmed (top-up), refund_pending
confirmed
Full amount validated; hash recorded
fiat_initiated
fiat_initiated
BasePSP layer triggered, exactly as for EVM
settled, refund_pending
settled
Fiat leg confirms; settleOrder invoked in Phase 2; reconciliation complete
terminal
refund_pending / refunded
Overpayment excess, expiry, or fiat failure; refund transfer or refundOrder submitted
terminal
manual_review
Unmatched or out-of-policy payment
any state above by audited operator action

4.8 Keys, webhooks, and the unchanged fiat leg
Stellar signing keys follow the existing operation-scoped secrets pattern (separate ed25519 keys for creation, settlement, refund, plus the aggregator contract identity), loaded from environment configuration, never logged, separated per environment. Stellar events reuse webhook_service.py and the HMAC signer with the standardized payload shape from the main document, so existing integrators change nothing. Confirmed Stellar payments hand off to the BasePSP fiat layer exactly as EVM payments do; M-Pesa, SasaPay, Cellulant and other rails see a confirmed order and an amount, never a chain.
5. Soroban Escrow Contract Workstream
The deliverable is a Soroban port of OrderManagement.sol, ElementPay's production escrow (UUPS proxy, OpenZeppelin) live today on Base mainnet and Base Sepolia: a Pending to Completed or Cancelled state machine with createOrder, settleOrder, and refundOrder gated by an aggregator-only authority, moving USDC between user, contract, and treasury. On Soroban the contract is Rust compiled to WASM against the soroban-sdk.

Figure 4. The escrow state machine and the direct Solidity-to-Soroban mapping for every mechanism in the contract.
5.1 Design points
	•	Custody flows through the USDC Stellar Asset Contract via the SEP-41 token interface; the contract is initialized against a specific SAC address, never an asset code, extending the issuer-verification rule into the contract layer.
	•	createOrder requires the payer's authorization (user-signed through Wallets Kit); settleOrder and pre-expiry refundOrder require the aggregator identity; refund after expiry is permissionless, the on-chain guarantee that user funds can never be stranded by ElementPay downtime.
	•	Order entries live in persistent storage keyed by order id; a TTL keeper worker extends the TTL of the contract instance, its WASM, and live entries ahead of state archival, with the watermark tracked in soroban_contracts. Settled and refunded entries archive naturally after a retention window; the ElementPay ledger remains the system of record.
	•	Upgradeability uses Soroban's native contract-Wasm-upgrade mechanism behind the aggregator authority, mirroring the role UUPS plays on EVM; a manual paused flag reproduces Pausable.
5.2 Testing and deployment discipline
	•	soroban-sdk unit tests cover the lifecycle plus adversarial cases: double settle, refund after settle, pre-expiry refund by a non-admin, duplicate order ids, custody conservation on every path.
	•	Property-based tests assert the two invariants that matter: contract custody always equals the sum of open orders, and no call sequence moves funds to anyone but treasury (settle) or the payer (refund).
	•	CI builds the WASM deterministically and pins the hash; testnet deployment is scripted with the Stellar CLI; the backend refuses to invoke a contract whose recorded hash it does not recognize.
	•	This is new surface area for the team (no Rust/Soroban in the stack today), so scope is conservative: testnet deployment and integration in Tranche 1, mainnet gated on the SCF audit-credit review in Tranche 3, with monitored transfers as the fallback rail throughout.
6. Stellar Disbursement Platform Workstream
SDP is the heaviest single deployment in the program. It is self-hosted: its bundled services (dashboard, core API, transaction submission components) and its own PostgreSQL database run as additional containers behind the same Caddy reverse proxy, with environment separation matching the rest of the stack. That is net-new infrastructure: a second database engine, new images, new credentials, and backup and upgrade runbooks.
	•	Batch creation: A payout adapter in the Aggregator translates ElementPay payout batches into SDP disbursements: batch creation, recipient mapping, per-recipient identifiers, asset selection against the registry.
	•	Status and exceptions: A status sync worker polls disbursement and per-recipient state into disbursement_batches and disbursement_items, and drives per-recipient webhook events; exceptions (failed recipients, expired invitations, trustline problems) surface with retry and cancel actions.
	•	Reconciliation: Every disbursement and recipient payment maps to an ElementPay payout ID, business ID, recipient reference, and settlement state, with the Stellar hash recorded per recipient.

Figure 5. Deployment topology: the SDP stack is the one genuine addition to the deployment surface; everything else extends the existing image.
7. CCTP Workstream
CCTP moves native USDC between Stellar and supported chains through burn, attestation, and mint. The backend treats every transfer as a persisted state machine with idempotent guards on each phase, plus a treasury view of in-flight transfers so liquidity operations never count only settled balances.

Figure 6. CCTP transfer state machine with parked and review states; nothing is ever silently dropped.
8. Aquarius Workstream
Deliberately the lightest integration in the program: a scheduled worker fetches liquidity and routing data for the pairs relevant to Stellar settlement, persists snapshots on a defined refresh interval into liquidity_snapshots, and exposes internal dashboard and reporting views next to the EVM-side liquidity views that already exist. Snapshot history, not point-in-time reads, is what makes the data usable for settlement decisioning.
9. New Background Workers
Worker
Function
Horizon payments stream listener
Per-account streaming, validation, order resolution (4.6)
Soroban event ingester
Contract event polling with persisted cursor (4.6, 5)
Cursor checkpoint and backfill
Cursor persistence, restart backfill, stall detection on both paths
Trustline and reserve provisioner
Trustline verification and creation, XLM reserve floors (4.2)
Contract TTL keeper
Extend-TTL ahead of state archival for instance, WASM, live orders (5.1)
Fee-stats poller
Network fee conditions feeding fee and fee-bump strategy (4.1)
Stellar reconciliation sweep
Periodic compare of Horizon history and contract state against the ledger
CCTP attestation poller
Attestation retrieval, timeout detection, state transitions (7)
Aquarius snapshot job
Liquidity and routing capture on a defined interval (8)
SDP status sync
Disbursement and per-recipient state sync, exception surfacing (6)

10. API Surface Changes
	•	Create Stellar-supported invoice, collection, payout, and payout batch; rail selected per request, so integrators adopt Stellar by parameter, not a new SDK.
	•	Fetch transaction status, reconciliation record, and Stellar details mapped to ElementPay references, including contract order state and per-recipient batch status.
	•	Webhook events for pending, completed, failed, refunded, and settled states through existing infrastructure.
	•	Admin endpoints for the review queue, refund actions, CCTP visibility, and contract administration (settle, refund, TTL status), gated by the existing admin auth layer and audit-logged.
11. Testing and Reliability
	•	Integration tests for payment creation, memo resolution, trustline pre-checks, refunds, contract invocations, and monitoring follow the pytest structure of the existing B2B/B2C suites, run against Stellar testnet in CI.
	•	Contract testing per Section 5.2: unit, adversarial, and property-based custody invariants on pinned deterministic builds.
	•	Idempotency tested explicitly: stream replay, cursor overlap, backfill, and duplicate webhook delivery each produce exactly one state transition per real-world event.
Failure mode
Expected behavior
Horizon stream drops mid-payment
Reconnect with backoff; backfill from persisted cursor; no missed or duplicated payments
Soroban event window outruns a stalled cursor
Cursor-freshness alert fires first; ledger-metadata backfill recovers the gap
Payment with missing or unknown memo
Never matched by guesswork; manual review with operator resolve and refund
Underpayment / overpayment (Phase 1)
partially_paid hold with top-up window / settle plus flagged refund of the excess
tx_bad_seq on submission
Sequence refresh and rebuild; channel accounts prevent contention
Fee surge
Fee ceiling respected; stranded transactions fee-bumped; time bounds guarantee deterministic expiry
Invocation fails the simulation-diff guard
Submission blocked; local state refreshed from chain; unresolved conflicts to review
Order entry TTL approaching archival
TTL keeper extends ahead of the watermark; alert on extension failure
Recipient missing trustline on payout
Caught by pre-validation; recipient flagged in exceptions, funds never fail mid-flight
CCTP attestation delayed
Timeout threshold, alert, attestation_delayed state; resumable without data surgery
SDP recipient failure inside a batch
Per-recipient exception with retry and cancel; batch status stays accurate

	•	Load testing covers listener throughput under bursty volume and payout plus contract-call submission concurrency.
	•	Alerting reuses the existing hot-wallet and health-check pattern; new alerts: stream stalls, cursor staleness, reserve floors, TTL watermarks, attestation timeouts, review-queue depth.
12. Environment and Deployment
	•	Tranche 1 runs entirely against Stellar testnet (Horizon and Soroban RPC); the public network arrives with the Tranche 3 cutover, consistent with the dev / staging / testnet / production separation already enforced via configuration.
	•	StellarManager initializes in the same startup sequence as Web3Manager and the PSP system; the Aggregator's topology does not change.
	•	Contract deployment is scripted and hash-pinned; mainnet contract deployment additionally gated on the audit review, with Phase 1 carrying production traffic in the interim.
	•	Production hardening covers mainnet key ceremony under the per-operation scheme, mainnet trustline provisioning, alert threshold review, and the cutover checklist.
13. Development Transparency
A dedicated stellar-integration branch and changelog carries Stellar-specific commits on a weekly public cadence from T1 kickoff, distinct from general repository activity, so progress is independently verifiable throughout the grant period rather than only at tranche delivery.
14. What Does Not Change
	•	The EVM settlement path (Web3Manager, escrow contracts, event listener) is untouched; Stellar is a parallel rail, not a rewrite.
	•	The fiat PSP layer, routing engine, and all payment providers require no modification.
	•	The dashboard framework, webhook signing scheme, and reconciliation ledger are reused, not rebuilt.
	•	Core deployment infrastructure (Docker, Caddy, environment separation) is unchanged; the only additions to the surface are the self-hosted SDP stack and the deployed escrow contract.
