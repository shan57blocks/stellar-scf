Source: https://drive.google.com/file/d/1Hh5XZdz4pRGjRz_nyITmZWFB9qIwl7dD/view (Policywright-ARCHITECTURE.docx in https://drive.google.com/drive/folders/1DWKsFcLIS5HSGjCBP55rZBi-w0dut6kJ)

Policywright — Technical Architecture
SCF #44 · RFP Track · “OZ accounts policy builder” Maintained by XXIX Labs · License: MIT · Built in the open
Policywright is an AI-assisted toolkit — an MCP server, a Claude skill, and a policy synthesizer — that turns a Stellar/Soroban transaction a user has already performed (or simulated) into the least-privilege OpenZeppelin smart-account authorization that permits exactly that flow and nothing else. This document describes the system, its Stellar/Soroban integration in depth, the security model, decentralization and infrastructure, and the build plan.

1. Problem and Stellar use case
OpenZeppelin’s accounts framework for Soroban decomposes authorization for a smart account (a C… contract address) into three composable elements:
	•	Context rules — scope and lifetime bindings, e.g. “may call transfer() on USDC for one year.”
	•	Signers — the entities authorized to act under a rule.
	•	Policies — enforcement modules that add programmable constraints (spending limits, multisig thresholds, time windows). A single context rule attaches up to 5 policies, each evaluated through a defined lifecycle: install / can_enforce / enforce / uninstall. Stateful policies must segregate storage by both the smart-account address and the context-rule id.
This is expressive enough to support subscription billing, agent delegation, social recovery, and treasury rails from one primitive. The cost is authoring complexity: today, scoping a delegation means writing a Soroban contract that implements the Policy trait correctly, segregates storage, handles the lifecycle, and gets audited. That bar is too high for most application developers and effectively prohibitive for end users who want to hand a narrow capability to an agent or service.
Policywright’s thesis: the most powerful starting point is a transaction the user has already performed or simulated. By examining its effects — which contracts were called, which functions, which assets moved, in what amounts, in what order — the tool can derive a context rule plus the minimal policies that permit a future invocation of that same flow, and only that flow. “Record this sequence; generate a policy that allows exactly this and nothing else.”
This sits at the intersection of three Stellar 2026 priorities — AI/agent-readiness, smart-account (C-address) adoption, and developer experience — and is defensively useful: an agent operating under a tightly scoped policy is categorically safer than an agent holding full account keys.
Prior art. Tyler (kalepail) built kalepail/pollywallet as an MVP proving the record-and-generate concept. Policywright adopts that concept and extends it to production: an audited synthesizer, an MCP server, a Claude skill, an argument-aware scope model, a deny-case simulation harness, and a real wallet install flow. We coordinate with Tyler to avoid duplication, and with the OpenZeppelin accounts maintainers as technical reviewers.

2. System overview

Policywright architecture: record → synthesize → emit → dry-run → install
The pipeline is code-first, deploy-second: the primary output is human-readable, reviewable policy code and a machine-readable spec. Deployment is always a separate, explicit step performed by the user or by an agent acting under existing permissions.

3. Components
3.1 Recording / observation layer
Two adapters yield a single normalized RecordedTx (contracts invoked, functions, decoded arguments, and token movements):
	•	Live by hash — queries Soroban RPC getTransaction(hash), decodes the transaction envelope (the InvokeHostFunction operation → invokeContract: contract address, function name, arguments as ScVals), and reads contract events from the result meta to reconstruct token movements (SEP-41 / Stellar Asset Contract transfer events: topics [transfer, from, to], data = amount as i128). ScVals are decoded with the SDK’s scValToNative. Soroban RPC retains only recent ledgers; an indexer adapter (same interface) covers older history.
	•	Simulated — ingests a transaction simulated via simulateTransaction against current or forked state, so a user can scope a flow before ever submitting it.
The layer also reads the SorobanAuthorizationEntry tree where present, to understand the sub-invocations a transaction authorized.
3.2 Synthesizer (core)
Converts a RecordedTx into the smallest authorization that still permits the observed flow:
	•	Scope binds exactly the (contract, function) pairs observed — never a third.
	•	Spending limits derive from gross outflow per asset (the maximum the delegate caused to leave the account), not net flow. A claim that brings in 175 BLND and a swap that sends 175 BLND out nets to zero, but the delegate still moved 175 BLND out and must be capped there. Assets only received get no cap (minimal permission).
	•	Lifetime and frequency constraints (e.g. one swap per 24h) bound the rule in time and rate.
	•	Argument constraints (configurable) — the synthesizer can derive constraints on observed call arguments (e.g. a swap path), so a BLND→USDC delegation does not also permit BLND→XLM. When disabled, the dry-run flags the gap explicitly.
	•	Compose-first / generate-second — the synthesizer configures stock OZ policies (spending_limit, simple_threshold, weighted_threshold) wherever they express the constraint, and only generates a fresh Policy contract where they cannot.
3.3 Emitter
Renders the result three ways: a machine-readable spec (the JSON an MCP client/agent consumes), a human-readable summary, and generated Rust for any net-new policy. Generated contracts implement the OZ Policy lifecycle (install / can_enforce / enforce / uninstall) with storage keyed by (smart_account, context_rule_id) so state never bleeds across accounts or rules.
3.4 Dry-run / simulation harness
Evaluates a generated policy against (a) the original recorded transaction (must permit) and (b) auto-generated adjacent transactions that must deny — same operations with a different asset, a larger amount, an out-of-window timestamp, or a repeated call beyond the frequency cap — plus flagging cases that are permitted but arguably too broad. It combines a fast local policy-semantics evaluator (for deny-case generation) with Soroban simulateTransaction for realistic on-chain checks. This lets a user confirm the policy is neither too strict nor too permissive before installing.
3.5 MCP server
Exposes record, synthesize, simulate, and verify as Model Context Protocol tools with structured inputs/outputs, deterministic behavior, and machine-readable error codes, so an AI agent can both request a policy be drafted from a sample transaction and reason about it before install. Self-hostable; runs locally alongside the developer or agent.
3.6 Claude skill
A conversational wrapper over the MCP server: “the user wants to grant permission to do X; here is a transaction they performed; draft a policy.” The skill knows when to ask for clarification (e.g. “this transferred 50 USDC — cap at 50, or allow up to 100 over a week?”). Packaged for Claude and structured so the same MCP backs other agent frameworks.
3.7 Wallet integration
A reference integration with a Stellar smart-account-supporting wallet from the C-Address Tooling cohort closes the loop: record → generate → simulate → sign → install on a real Soroban smart account. The install transaction is signed client-side; secret keys never leave the client.

4. Stellar / Soroban integration summary
Touchpoint
How Policywright uses it
Soroban smart accounts (C-addresses)
The subject of every generated authorization; install target for policies
OZ accounts package
Context rules, signers, policies; Policy trait + lifecycle; ≤5 policies/rule; storage segregation
OZ stock policies
spending_limit, simple_threshold, weighted_threshold composed where they fit
Soroban RPC
getTransaction (live recording), simulateTransaction (dry-run + simulated recording)
XDR / ScVal
Decode envelope, invokeContract args, and contract events via the Stellar SDK
Contract events (SEP-41 / SAC)
Reconstruct token movements (transfer topics + i128 amount)
stellar-cli + soroban-sdk
Compile generated Policy contracts; deploy to testnet/mainnet; reproducible builds via digest-pinned image
stellar-wallets-kit / C-Address cohort wallet
End-to-end install of a generated policy on a real smart account
Stellar is not a superficial layer here — the entire product is Soroban smart-account authorization tooling. Policywright targets the most recent stable releases of soroban-sdk, stellar-cli, the Stellar JS SDK, and the OZ accounts package, and tracks them as they advance.

5. Decentralization
Policywright is local-first and self-hostable, with no custodial backend:
	•	The synthesizer and emitter are stateless and run locally (CLI) or as a self-hosted MCP server — there is no central service that sees user funds or keys.
	•	Recording and simulation run against any Soroban RPC endpoint, including a node the user runs themselves; no hardcoded provider.
	•	Generated policies are user-owned; the user (or an agent under existing permissions) reviews and deploys them. Policywright never deploys on a user’s behalf.
	•	The full codebase is open source (MIT) and self-hostable; the MCP server can be run by each developer or agent independently.
There is no protocol-level component to decentralize further — the tool produces artifacts the user controls and runs on infrastructure the user chooses.

6. Infrastructure
	•	Core synthesis: Node/TypeScript, runs locally (CLI) or as a self-hosted MCP server; stateless, no mandatory database.
	•	Chain access: Soroban RPC (public or self-run) for live-tx ingestion and simulateTransaction.
	•	Build toolchain: Rust + stellar-cli, with reproducible policy builds in a digest-pinned container.
	•	Distribution: npm for the SDK / MCP server; a packaged skill for Claude.
	•	Optional: a testnet demo deployment for evaluation, and a lightweight local cache — neither is required to use the tool.
No always-on hosted service holds user funds or keys, which keeps the operational and trust surface minimal.

7. Security model
This tool generates code that runs as authorization logic on user funds, so security is treated as a first-class deliverable:
	•	No key custody. Keys never leave the client; install transactions are signed client-side.
	•	Code-first, deploy-second. The user reviews (and may modify) generated code before any deployment; nothing deploys automatically.
	•	Deny-case verification. The dry-run harness generates and tests adjacent transactions that should fail (different asset, larger amount, out-of-window, repeated call), so over-permissive policies are caught before install.
	•	Storage segregation. Stateful generated policies key storage by (smart_account, context_rule_id), preventing state collisions across accounts or rules.
	•	Audit of the synthesizer itself. We commit to an audit of the synthesizer logic and generated policy templates — not just sample outputs — via the SCF Audit Bank (audit credits are provided at Tranche #3 completion and are not in our budget). Until audited, generated contracts are clearly labelled illustrative and not for production deployment.
Threat model (addressed in the audit-readiness deliverable): malicious or malformed input transactions; over-broad scope inference; storage collisions; policy bypass; and incorrect lifecycle handling. The threat model and audit scope are submitted to the Audit Bank as part of Tranche 3.

8. User tracking and user protection
Policywright is a developer tool, not a consumer service:
	•	No PII collection and no key custody.
	•	Any usage analytics are opt-in, anonymized, and off by default.
	•	The product’s purpose is user protection — least-privilege scoping, reviewable generated code, and deny-case simulation are themselves protective UX. Delegating a narrow, simulated-and-reviewed capability is far safer than handing an agent full keys.

9. Maintenance and community
	•	Open source (MIT), built in the open — public repo, semantic versioning, CI (typecheck + tests + demo), and an open issue tracker from day one.
	•	Versioned distribution — a versioned MCP server endpoint and a packaged Claude skill, so downstream consumers upgrade predictably.
	•	Docs contributed to the Stellar Developer Docs, plus in-repo developer documentation (how the synthesizer makes scoping decisions; how to extend it with new policy primitives).
	•	Ecosystem coordination — OZ accounts maintainers as technical reviewers (validating generated-code quality and identifying primitives worth upstreaming), the C-Address Tooling cohort for wallet integration, and Tyler/kalepail to avoid duplication.
	•	Community updates via the Stellar Dev Discord (#scf channels) and open channels (Mastodon / BlueSky), with regular status posts.
	•	Funding tail — the core is stateless and local, so ongoing cost is low; XXIX Labs maintains Policywright as part of its developer-tooling portfolio after the grant.

10. Build plan (summary)
Tranche
Focus
Headline outcome
T1 — MVP (testnet)
Recording layer (live + simulated), synthesizer (scope + composed policies + minimal-permission), policy compile + testnet deploy, open-source CLI + CI
A recorded testnet flow produces a context rule + composed OZ policy; generated policy compiles and deploys to testnet
T2 — Testnet expansion
MCP server, Claude skill, dry-run harness + argument-level scope, net-new policy codegen with storage segregation, wallet integration
End-to-end record → generate → simulate → install on a testnet smart account, agent-driven via MCP + skill
T3 — Mainnet launch
Three documented walkthroughs, OZ validation, production release (versioned MCP + packaged skill + docs), mainnet demonstration, test suite + audit readiness
A generated policy installed on a mainnet Soroban smart account; production release; audit scope submitted to Audit Bank
Detailed deliverables, success criteria, budgets, and dates are in the submission form.

Policywright extends kalepail/pollywallet and is developed in coordination with the OpenZeppelin accounts maintainers (as technical reviewers) and the C-Address Tooling cohort. All repositories are MIT-licensed and developed in the open.
