# Policywright: Record-to-Policy MCP + Agent skill

Source: https://communityfund.stellar.org/submissions/recdYirBtCwecAipc (SCF #44, awarded $55.0K, category: Developer Tooling)

- Website: https://github.com/kunaldrall29/policywright
- Architecture doc: https://drive.google.com/drive/folders/1DWKsFcLIS5HSGjCBP55rZBi-w0dut6kJ?usp=sharing

## Links in the submission

- https://github.com/kunaldrall29/policywright
- https://drive.google.com/drive/folders/1DWKsFcLIS5HSGjCBP55rZBi-w0dut6kJ?usp=sharing

## Submission text (as published)

```text
Products & Services
Policywright is an AI-assisted toolkit that turns a Stellar/Soroban transaction a user has performed or simulated into the least-privilege OpenZeppelin smart-account authorization that permits exactly that flow. Its components:
Transaction recorder (live + simulated).
 Ingests a real transaction by hash via Soroban RPC
getTransaction
, or a
simulateTransaction
 result, decoding the envelope and contract events (XDR/ScVal) into structured contracts, functions, arguments, and token movements.
Stellar:
 reads Soroban transaction meta and SEP-41/SAC
transfer
 events directly.
Impact:
 converts a real on-chain smart-account flow into machine-readable intent — the input to everything else.
Least-privilege synthesizer.
 Derives a context rule scoped to exactly the
(contract, function)
 pairs observed, with spend caps from gross outflow, lifetime/frequency bounds, and optional argument constraints.
Stellar:
 maps directly to OZ accounts context rules + policies.
Impact:
 makes Soroban smart-account authorization authorable without hand-writing a Policy contract.
Compose-first / generate-second policy engine.
 Configures stock OZ policies (
spending_limit
,
simple_threshold
,
weighted_threshold
) where they fit, and generates fresh Soroban
Policy
 contracts (Rust) — correct
install/can_enforce/enforce/uninstall
 lifecycle, storage segregated by
(smart_account, context_rule_id)
 — where they don't.
Stellar:
 emits compilable Soroban contracts using soroban-sdk.
Impact:
 produces auditable on-chain authorization logic, reusing ecosystem primitives first.
Dry-run simulation harness.
 Tests a generated policy against the original flow (must permit) and auto-generated adjacent transactions (must deny: different asset, larger amount, out-of-window, repeated call), and flags too-broad results.
Stellar:
 uses Soroban
simulateTransaction
 plus a policy-semantics evaluator.
Impact:
 users verify scoping before installing on a real smart account.
MCP server.
 Exposes record/synthesize/simulate/verify to agents over the Model Context Protocol with deterministic, structured I/O.
Stellar:
 makes Soroban smart-account delegation agent-native.
Impact:
 directly serves the 2026 AI/agent-readiness priority.
Agent skill.
 A conversational entry point that wraps the MCP and asks for clarification when scope is ambiguous.
Stellar:
 lets non-expert developers/users generate Soroban policies in natural language.
Impact:
 widens who can safely delegate on Stellar.
Wallet integration (C-Address cohort wallet).
 End-to-end record → generate → simulate → sign → install on a real Soroban smart account, signed client-side.
Stellar:
 installs a policy on a
C…
 smart account via stellar-wallets-kit.
Impact:
 closes the loop to a deployed, installed, least-privilege authorization.
Policywright extends
kalepail/pollywallet
 to production and is developed with the OpenZeppelin accounts maintainers as technical reviewers.
Technical stack, decentralization, and operations
Stack. TypeScript/Node for the synthesizer core, MCP server, and CLI; Rust/soroban-sdk for generated Soroban Policy contracts; built and deployed with stellar-cli. Targets the latest stable Soroban, stellar-cli, soroban-sdk, Stellar JS SDK, and OpenZeppelin accounts releases. A diagram and full stack are in the linked Technical Architecture document.
Decentralization. Local-first and self-hostable, with no custodial backend. The synthesizer and MCP server are stateless and run on the user's machine; recording and simulation work against any Soroban RPC endpoint, including a self-run node. Generated policies are user-owned and deployed by the user (or an agent under existing permissions) — Policywright never deploys on a user's behalf, and no central service holds funds or keys.
Infrastructure. Stateless core (local CLI or self-hosted MCP server); Soroban RPC (public or self-run) for getTransaction and simulateTransaction; reproducible policy builds via a digest-pinned container with stellar-cli; npm for distribution of the SDK/MCP and a packaged Agent skill. No mandatory database or always-on hosted service.
User tracking and protection. No PII and no key custody; install transactions are signed client-side and secret keys never leave the client. Any usage analytics are opt-in, anonymized, and off by default. The tool's purpose is protective — least-privilege scoping, reviewable generated code, and deny-case simulation make delegating a narrow, verified capability far safer than handing an agent full keys.
Maintenance and community. MIT-licensed and built in the open: public repo, semantic versioning, CI (typecheck + tests + demo), and an open issue tracker from day one; a versioned MCP endpoint and packaged skill; developer docs contributed to the Stellar Developer Docs; coordination with OpenZeppelin to upstream useful primitives. Regular status updates in the Stellar Dev Discord (#scf) and via open channels (Mastodon / BlueSky). The stateless, local design keeps ongoing cost low; XXIX Labs maintains Policywright as part of its developer-tooling portfolio.
Differentiation. Today, scoping a Soroban delegation means hand-writing and auditing a Policy contract — prohibitive for most developers, impossible for end users delegating to an agent. Policywright generates that authorization from a transaction the user already performed, composing stock OZ policies first and generating new ones only when needed, and verifies scope with a deny-case dry-run before install. It extends the kalepail/pollywallet MVP to production and adds the agent-native surface (MCP + skill) that makes scoped delegation usable by AI agents.
Requested Budget
$55.0K
Traction Evidence
Active SCF #42 Build Award ($75K) — Nectar Network.
 Tranche 1 delivered and passed review; Tranche 2 in review (Soroban DEX integration, multi-protocol Rust adapters, indexer/dashboard). Evidence we ship on Stellar and complete tranches on schedule.
[https://nectarnetwork.fun]
Web3 Foundation grant, delivered — PowerGrid Network.
 Four ink!/Rust contracts live on mainnet, with hardware (IoT) integration and an oracle.
[https://github.com/kunal-drall/powergrid_network]
Working Policywright v0 spike (this submission).
 A runnable record → synthesize → dry-run pipeline: from a Blend claim → swap-to-USDC transaction it generates a least-privilege context rule, composes an OZ
spending_limit
, generates a net-new frequency-limit Soroban
Policy
 contract, and passes 7/7 permit/deny/flag dry-run cases. Strict TypeScript, clean typecheck, MIT, open source.
Tranche 1 (Deliverable Roadmap) - MVP
Hardened recording layer (live + simulated).
 Live-by-hash via Soroban RPC
getTransaction
 and simulated-tx ingestion via
simulateTransaction
, extracting contracts, functions, decoded arguments, and token movements into a typed
RecordedTx
.
Completion:
 CLI ingests a real
testnet
 Blend claim → swap transaction by hash and outputs a structured
RecordedTx
; short demo recorded.
Budget: $5,000.
Least-privilege synthesizer (scope + composed policies).
 Context rule scoped to observed
(contract, function)
 pairs, with gross-outflow spend caps and the minimal-permission inflow rule; composes a stock OZ
spending_limit
.
Completion:
 from the recorded tx, emits
context-rule.json
 + composed
spending_limit
 params; unit tests for scope, gross-vs-net, and inflow-only cases pass in CI.
Budget: $6,000.
Policy compilation + testnet deployment.
 A generated Soroban
Policy
 contract compiles via stellar-cli and deploys to testnet.
Completion:
 generated policy compiles and is deployed to testnet (contract ID shared).
Budget: $3,500.
Open-source CLI, repo, and CI.
 Public MIT repo with README, the v0 spike extended to a robust CLI, and CI running typecheck + tests + demo.
Completion:
 public repo with green CI;
npm run demo
 produces artifacts.
Budget: $2,000.
Tranche 2 (Deliverable Roadmap) - Testnet
MCP server.
 Exposes
record
,
synthesize
,
simulate
,
verify
 over MCP with deterministic, structured I/O and machine-readable errors.
Completion:
 server runs locally; an agent calls each tool; reference session recorded.
Budget: $4,500.
Agent skill.
 Conversational wrapper over the MCP with clarification prompts.
Completion:
 skill packaged; demo of "grant permission to do X from this transaction" producing a reviewed policy.
Budget: $3,000.
Dry-run harness + argument-level scope.
 Permit-case + auto-generated deny-cases (different asset, larger amount, out-of-window, repeated call) and configurable argument constraints (e.g. swap
path
).
Completion:
 harness outputs a permit/deny/flag report including an argument-constrained policy; tests green.
Budget: $3,500.
Net-new policy codegen with storage segregation.
 Generates stateful
Policy
 contracts with storage keyed by
(smart_account, context_rule_id)
; compose-first/generate-second mode.
Completion:
 generates both a composed-policy config and a net-new stateful policy contract; both compile and pass simulation.
Budget: $2,500.
Wallet integration (testnet, end-to-end).
 Record → generate → simulate → sign → install on a
testnet
 Soroban smart account via a C-Address cohort wallet, signed client-side.
Completion:
 testnet smart account with an installed generated policy; end-to-end demo recorded.
Budget: $3,000.
Tranche 3 (Deliverable Roadmap) - Mainnet
Three documented end-to-end walkthroughs.
 Blend yield-claim, SEP-41 subscription, and bounded Soroswap delegation (or equivalents).
Completion:
 three published, runnable walkthroughs; recorded.
Budget: $5,000.
OpenZeppelin validation + coordination.
 OZ accounts maintainers review generated-code quality as technical reviewers; identify primitives worth upstreaming.
Completion:
 OZ review notes incorporated; summary of validated templates and upstream proposals.
Budget: $3,000.
Production release.
 Versioned MCP server endpoint + packaged Agent skill (npm + skill package), semantic versioning, and docs contributed to the Stellar Developer Docs.
Completion:
 tagged release, published packages, docs PR opened.
Budget: $5,500.
Mainnet demonstration.
 Generate → simulate → install a policy on a
mainnet
 Soroban smart account via the integrated wallet.
Completion:
 mainnet smart account with an installed generated policy (contract ID + transaction links).
Budget: $5,500.
Test suite + audit readiness.
 Comprehensive tests across synthesizer input shapes; threat model and audit-prep for the synthesizer + generated templates, with audit scope submitted to the Audit Bank (audit itself not budgeted).
Completion:
 coverage report; threat-model doc; audit scope submitted to Audit Bank.
Budget: $3,000.
Team
Kunal
 — Founder/CEO, project lead. Stellar India Ambassador; Rust, Soroban, TypeScript; applied cryptography and zero-knowledge; AI-augmented build workflow (Claude Code, Codex, Cursor, MCPs). Delivered a Web3 Foundation grant (PowerGrid, mainnet) and leads the active SCF #42 award (Nectar Network).
LinkedIn
Daksh
 — Smart contracts & backend (Rust / Soroban); Stellar India Ambassador; second program participant for the webinars, bootcamp, and demo day per SCF requirements, and review support on the generated Soroban Policy contracts.
LinkedIn
Daksh drall
Kunal
```
