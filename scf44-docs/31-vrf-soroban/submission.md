# VRF-Soroban: Soroban VRF

Source: https://communityfund.stellar.org/submissions/recHHh2SgBoGOtcZ3 (SCF #44, awarded $50.0K, category: Developer Tooling)

- Website: https://github.com/NibrasD/Stellar-VRF
- Architecture doc: https://docs.google.com/document/d/1EwEmEiNicr-QzThme4nyjk3Dz2KMerTsEiPvny_7Hy0/edit?usp=sharing

## Links in the submission

- https://github.com/NibrasD/Stellar-VRF
- https://docs.google.com/document/d/1EwEmEiNicr-QzThme4nyjk3Dz2KMerTsEiPvny_7Hy0/edit?usp=sharing
- https://stellar.expert/explorer/testnet/contract/CAUL2Y45FMGRSELVIO2QVNNJ4GQ4SQADY2QYTZCRW7K6YINU5QWLD2UT
- https://github.com/NibrasD/stellar-transaction-visualizer
- https://github.com/NibrasD/stellar-hummingbot-connector
- https://github.com/NibrasD/Stellar-Concentrated-Liquidity

## Submission text (as published)

```text
Products & Services
Current status (MVP):
We currently have a simple MVP that proves feasibility on testnet (basic request, fulfillment, and deterministic derive flow). The plan below describes what we will build for production/mainnet.
The existing MVP serves as technical validation only and does not yet include the full production feature set described in this here (Products & Services) and in the Technical Architecture.
1) BLS-VRF Oracle Contract (Pay-per-Request)
What we will build:
 A production Soroban contract that acts as a native verifiable randomness oracle based on a pairing-based BLS-VRF construction. It accepts randomness requests from dApps, collects escrowed fees, verifies BLS-VRF proofs on-chain, and delivers deterministic randomness.
How Stellar is used:
 Soroban smart contract execution for proof verification, SAC token transfers for the pay-per-request escrow model, and Soroban state storage for the request lifecycle.
Impact on the project:
 Establishes a sustainable, self-funded oracle service. Moves randomness correctness verification from backend trust assumptions to on-chain cryptographic verification.
preventing Economic DoS (spam) aligning economic incentives for sustained oracle operation.
2) Native On-Chain Callback Integration
What we will build:
 A callback-compatible request path so consumer contracts receive verified randomness directly via Soroban cross-contract invocation.
How Stellar is used:
 Soroban’s cross-contract call model is the sole delivery mechanism. HTTP endpoints are used only for off-chain event listening, never as a trust boundary.
Impact on the project:
 Makes the protocol natively composable for games, NFT mints, and DAO workflows without relying on off-chain HTTP polling.
3) Future-Round drand Input Binding
What we will build:
 A mandatory protocol mechanism where every request is bound to a future drand round (
round_offset >= 2
) that does not yet exist at request time.
How Stellar is used:
 The contract deterministically computes the required future round from the ledger timestamp and stores it. Fulfillment is rejected if the submitted drand round does not match the locked future round.
Impact on the project: drand provides input unpredictability — by binding every request to a future drand round that does not yet exist, neither the oracle nor the requester can predict or influence the VRF input (alpha) at request time. The BLS-VRF layer transforms the public drand beacon into an oracle-specific verifiable output that cannot be computed by third parties before fulfillment. — even after the drand round is published, the final randomness (beta) remains hidden from the requester and all external observers until fulfillment, ensuring the randomness consumed by a dApp is a sealed, context-bound value rather than a public, shared beacon output. drand is not used as the randomness service itself; it is the anti-grinding input that, combined with the VRF, guarantees unpredictability throughout the entire request lifecycle — not merely at request time.
4) BLS-VRF Cryptographic Verification (Native Host Functions)
What we will build:
 On-chain verification of drand signatures and the oracle's BLS-VRF proof (
gamma = sk * H_G1(alpha)
, verified via bilinear pairing equations), plus strict domain-separated output derivation.
How Stellar is used:
 Exclusively utilizes Soroban's native BLS12-381 host cryptographic functions (CAP-0059, Protocol 22+) and subgroup validation (CAP-0080, Protocol 26). No WASM cryptography is used in the critical path.
Impact on the project:
 Preserves strict cryptographic security while staying well within Soroban's transaction budgets. The current MVP already executes the full on-chain verification pipeline at ~58M instructions on testnet — safely under the per-transaction limit with 42% headroom. The production version extends this with callback dispatch, timeout/refund logic, and operational hardening with optimization.
5) Liveness, Safety & Economic Controls
What we will build:
 Timeout/refund state transitions, fee escrow management, and operational monitoring for the oracle infrastructure.
How Stellar is used:
 Timeout/refund conditions and fee releases are enforced by on-chain state and ledger time constraints.
Impact on the project:
 Prevents stalled requests from trapping consumer funds, ensures oracle accountability for liveness, and provides capital safety in failure scenarios.
6) High-Availability Relay Infrastructure
What we will build:
 A high-availability relay infrastructure with primary/standby failover operated by the core team to ensure oracle liveness and prevent request timeouts. The system uses a single oracle operator with a hot-standby replica and automatic failover via leader election to prevent double-submission.
How Stellar is used:
 Relays listen to on-chain events, fetch future-bound drand beacons, and submit fulfillment transactions to the Soroban network via redundant RPC endpoints.
Impact on the project:
 Guarantees that the "pay-per-request" service is reliable. A single relay failure could lead to delayed fulfillments and timeout-triggered refunds. The HA infrastructure ensures consistent fulfillment, which is critical for mainnet adoption. The construction relies on the unique-signature property of BLS signatures: for a fixed public key and message, there exists exactly one valid proof γ. This prevents proof grinding and ensures deterministic verifiability.
7) Developer SDK & Integration Tooling
What we will build:
 Client libraries (JavaScript and Rust) that abstract the VRF contract interaction — request submission, callback handling, randomness derivation, and error management — into simple, type-safe developer APIs. The SDK also includes consumer contract examples and integration guides.
How Stellar is used:
 The SDKs wrap
@stellar/stellar-sdk
 (JS) and
soroban-sdk
 (Rust) to construct and submit Soroban transactions targeting the VRF contract, handle XDR encoding/decoding, and manage Soroban RPC interactions.
Impact on the project:
 Lowers the integration barrier for dApp developers, accelerating ecosystem adoption. Without SDKs, every consumer must manually construct XDR payloads and handle Soroban RPC interactions, significantly increasing development time and error surface.
The construction relies on the unique-signature property of BLS signatures: for a fixed public key and message, there exists exactly one valid proof γ. This prevents proof grinding and ensures deterministic verifiability.
Requested Budget
$50.0K
Traction Evidence
Our primary traction at this stage is
achieving a functional Proof-of-Concept (PoC) on Stellar Testnet
, validating that rigorous cryptographic primitives can run efficiently on Soroban.
The demand is clear: hundreds of on-chain apps require verifiable randomness as standard infrastructure. However, the bottleneck has always been executing the math on-chain. We have bypassed this bottleneck.
Our MVP is live:
https://stellar.expert/explorer/testnet/contract/CAUL2Y45FMGRSELVIO2QVNNJ4GQ4SQADY2QYTZCRW7K6YINU5QWLD2UT
Tranche 1 (Deliverable Roadmap) - MVP
Goal:
 Build the production-grade Soroban VRF contract core and oracle fulfillment pipeline.
Completion Criteria
Core Soroban VRF contract implemented — all functions excluding callback invocation (
init
,
request
,
request_with_callback
 stub,
fulfill
,
derive_random
,
derive_random_in_range
,
timeout_refund
,
cleanup_proof
)
request_with_callback
 included as stub — stores callback metadata on-chain; invocation logic deferred to Milestone 2
Future-round drand binding enforced (round_offset ≥ 2)
BLS-VRF on-chain verification implemented using native Soroban host functions (CAP-0059):
bls12_381_pairing_check
,
bls12_381_hash_to_g1
,
bls12_381_g1_mul
Storage TTL extension implemented within
request()
 to prevent state expiration during the fulfillment window
Single-node oracle worker service operational on testnet
End-to-end request → fulfill → derive flow validated on testnet
On-chain instruction budget measured from testnet profiling and confirmed below 70M target (58M estimated)
Budget
Production contract implementation (core VRF logic, drand binding, storage TTL, full API minus callback invocation) — $4,500
Oracle worker service (event listener, drand integration, fulfillment pipeline, retry logic) — $3,500
Testnet deployment & integration testing (E2E flow validation, on-chain instruction profiling, storage cost measurement) — $2,000
Total — $10,000
Tranche 2 (Deliverable Roadmap) - Testnet
Goal:
 Complete protocol composability layer and validate security assumptions.
Completion Criteria
Callback invocation implemented inside
fulfill()
 following the Checks-Effects-Interactions pattern (state committed before cross-contract call)
Consumer authorization model implemented and documented (VRF contract as caller, not original requester)
Consumer contract example library published
Timeout/refund state machine implemented and tested
Storage TTL edge cases hardened (
cleanup_proof
 interaction with
Fulfilled
 flag retention, TTL on fulfilled entries)
Key rotation support implemented (
rotate_oracle_keys
,
rotate_drand_pk
) — deferred from M1 as non-critical-path security hardening; mandatory before mainnet deployment
Threat model documented and validated including single-oracle trust boundary (liveness vs. bias)
Failure scenarios tested: oracle downtime, delayed drand rounds, duplicate fulfillment attempts, invalid signatures, malicious callback re-entrancy
fulfill()
 instruction budget profiled from M1 testnet data and optimized: storage layout packing, SAC token transfer path, G1 negation host function cost validation,
derive_random_in_range
 rejection sampling worst-case bounded
All code paths confirmed below 70M instruction target
Budget
Callback invocation implementation & consumer contract example library — $3,500
Timeout/refund state machine & storage TTL edge-case hardening — $3,500
Security testing & threat model documentation — $4,000
Performance profiling & optimization (storage layout, G1 negation cost, rejection sampling bound, SAC transfer path) — $4,000
Total — $15,000
Tranche 3 (Deliverable Roadmap) - Mainnet
Goal:
 Deploy and operate a production-ready randomness oracle on Stellar Mainnet.
Completion Criteria
Mainnet deployment completed
Primary oracle worker running in production with hot-standby replica and automatic failover
Leader election implemented to prevent double-submission from redundant nodes
Developer SDK released (JS and Rust)
Example integration dApp published
Operational procedures documented
Public mainnet transactions available as proof of operation
Public dashboard for oracle activity published
Interactive randomness playground deployed
Budget
Mainnet deployment & configuration — $2,000
Primary + standby oracle deployment & failover engineering (hot-standby replica, leader election, automatic failover for single-operator HA) — $5,000
Production monitoring, alerting, drand delay detection & retry logic — $4,000
Developer SDK (JS/Rust) & integration tooling — $3,000
Mainnet hardening (drand key transitions, recovery procedures, edge cases) — $4,000
Operational runbooks & incident response documentation — $4,000
Public dashboard, explorer & randomness playground development — $3,000
Total — $25,000
Team
The team has good experience in the
Stellar ecosystem
, including deep protocol-level understanding, Soroban smart contract development. In addition, a working
MVP for SorobanVRF
 has already been developed.
Stellar Transaction Visualizer
https://github.com/NibrasD/stellar-transaction-visualizer
Key Expertise:
Deep understanding of
Stellar protocol and Soroban smart contracts
Blockchain data visualization and
SDK development
Smart contract analysis (cross-contract calls, resource usage: CPU, fees, memory)
Transaction simulation and pre-deployment testing
Stellar Hummingbot Connector
https://github.com/NibrasD/stellar-hummingbot-connector
Key Expertise:
Algorithmic trading (market making, arbitrage)
Blockchain exchange integration (Stellar DEX + external trading systems)
Stellar RPC & Soroban RPC integration
XDR transaction encoding/decoding
Stellar-Concentrated-Liquidity
https://github.com/NibrasD/Stellar-Concentrated-Liquidity
Advanced AMM cryptography & secure swap mathematics
Tick-based price encoding with 256-bit arithmetic safety
Hook-based extensible execution model (secure lifecycle callbacks)
MEV-aware liquidity design and swap execution integrity
Smart contract security on Soroban (authorization, overflow protection, tick validation)
Worked as well on some projects such as:
Commit-reveal schemes:
 Used for MEV-resistant auctions and private payments (NEO-FairAuction, HashPay)
Zero-Knowledge Proofs (ZK-SNARKs, Groth16):
 Applied in confidential auctions and escrow systems (ZK-Escrow-Pro).
Privacy-preserving financial protocols:
 Secure bidding, escrow, and settlement systems with hidden transaction data.
Advanced smart contract security:
 Solidity-based designs focused on MEV resistance, confidentiality, and secure identity handling.
Github:
https://github.com/NibrasD
Mostafa Lotfy:
https://www.linkedin.com/in/mostafalotfy2/
Gemy Lotfy:
https://www.linkedin.com/in/gemy-lotfy/
Gemy
```
