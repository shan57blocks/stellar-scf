# CredioLabs.AI: OZ Policy Builder by Crediolabs.ai

Source: https://communityfund.stellar.org/submissions/recUpUVSNstKqoxlm (SCF #44, awarded $125.0K, category: Developer Tooling)

- Website: https://crediolabs.ai/
- Architecture doc: https://app.notion.com/p/untangledfi/OZ-Accounts-Policy-Builder-by-Crediolabs-ai-37f08dd274ad80c595a7db54f2b62cdf?source=copy_link

## Links in the submission

- https://app.notion.com/p/untangledfi/OZ-Accounts-Policy-Builder-by-Crediolabs-ai-37f08dd274ad80c595a7db54f2b62cdf?source=copy_link
- https://github.com/kalepail/pollywallet

## Submission text (as published)

```text
Products & Services
The OZ Accounts Policy Builder is a Model Context Protocol (MCP) server and companion Claude skill that closes a critical gap in the OpenZeppelin Stellar Accounts framework. Today, a developer who wants to grant an AI agent permission to perform a bounded on-chain action — claim Blend yield, pay a SEP-41 subscription, run a bounded SoroSwap trade — must write a context rule and Policy trait implementation by hand. Our tool automates this: it records a Soroban transaction, synthesises the minimal context rule and policy against the OZ Accounts primitives, runs a 7-dimension deny-case harness to verify the policy denies the right adjacent transactions, and presents the result for human review via a Claude skill before installing on-chain via Freighter.
Five MCP tools:
record_transaction
,
synthesize_policy
,
simulate_policy
,
verify_policy
,
install_policy
. Built on: 2 audited Soroban contracts (OctoLend / Runtime Verification March 2026; Untangled Vault / Veridise May 2025), the OctoPos production position tracker (7 protocols), and the Untangled Loop, synthetic perpetual adapter built on Blend and Aquarius.
Building on kalepail/pollywallet:
 Tyler's
kalepail/pollywallet
 demonstrated the core record-and-generate concept as an MVP in under a week. We treat it as the starting point and scope our work around bringing it to production quality. What we adopt: the record-and-generate workflow and the core insight that an observed transaction sequence can specify a minimal policy. What we extend: minimality verification (strip-and-verify loop), a 7-dimension deny-case harness derived from our own independent Soroban audits rather than from specification reading, Path B code generation for novel constraint patterns with a mandatory
cargo check
 gate, a full MCP server with Zod-validated schemas and elicitation-before-action design following the Cloudflare Agent Setup pattern for structured plugin/skill composition, Claude agent skill, Freighter end-to-end wallet integration for the full record → generate → simulate → sign → install flow, three documented walkthroughs with live testnet receipts, and an independent audit of the synthesizer logic and generated templates. Our XDR parsing layer re-implements against the
stellar_xdr
 crate already in production use within OctoPos — a direct extension of existing infrastructure, not a greenfield rewrite.
Two mechanisms go beyond standard delivery:
1. Verifiably minimal generated code.
 The
minimality checker
 is a strip-and-verify loop: remove each constraint, rerun the deny-case suite, keep only what is load-bearing. The
7-dimension deny-case harness
 — amount variation, asset substitution, contract substitution, function substitution, timing violations, time-window violations, policy-capacity violations — was designed from findings in our own independent Soroban audits, not from reading the OZ specification. Runtime Verification (OctoLend, March 2026) and Veridise (Untangled Vault, May 2025) each surfaced the exact authorization bypass and storage key scoping failures that a generic synthesiser would silently reproduce. Our harness tests for those failure modes specifically because we encountered them in production Rust and had them fixed under auditor review.
2. Platform-maintained at
agents.crediolabs.ai
 The policy builder is deployed as a live service at
agents.crediolabs.ai
 — accessible via browser or REST API, no local installation required. It runs on the same Kubernetes stack as OctoPos in production: monthly
cargo audit
/
pnpm audit
 sweeps, Stellar protocol upgrade compatibility testing within one release of each testnet candidate. When a Soroban XDR format shift breaks the transaction recorder, the signal lands in an existing monitoring system with a platform service accountable — not in a dormant GitHub repository.
Requested Budget
$125.0K
Traction Evidence
OctoPos
 — production Stellar DeFi position tracker on mainnet. 7 protocol adapters (Blend, Aquarius, SoroSwap, Phoenix, FxDAO, Stellar Wallet, Untangled Vault), 7 price feeds. In use by curators including Gami Capital, Stake Capital, and Indentura. OctoPos transaction-builder already parses Soroban XDR and constructs transactions against Blend, SoroSwap, Phoenix, and FxDAO — this infrastructure feeds directly into the policy recorder.
OctoGear MCP server
 — first production MCP server Credio Labs has built. 57 tools (31 read / 17 write / 9 intent) for OctoGear's EVM prime brokerage product across Polygon, HyperEVM, and Hyperliquid L1. Fail-closed trust model: self-verify gate (sigstore + bytecode hash + tool descriptor hash) at boot; 3-gate request pipeline (identity token → client fingerprint → pause-until-handshake); dual pause layers (handshake gate + enforcer risk state machine); HMAC WebSocket bridge for policy updates and OOB confirmations; hash-chained audit log. The intent-path pattern — signed intent built by agent, confirmed by owner passkey via OS notification before any fund movement — is the direct precedent for the OZ Policy Builder's
install_policy
 → elicitation → Freighter sign flow.
Celo Tax & Portfolio Agent
 — second MCP server, shipped June 2026 for a Onchain Agents Hackathon. 7 MCP tools; 6-stage orchestrated pipeline (fetch → classify → price-enrich �� computePnl → exportCsv → answerQuery); TypeScript ESM; rule-based classification with LLM fallback (Anthropic SDK, Claude Haiku 4.5); hand-rolled JSON-RPC 2.0 over stdio (zero SDK dependency, written around a confirmed Zod compat bug in the official SDK). ERC-8004 registered on Ethereum and Celo. The orchestrated sub-agent pattern — one fixed pipeline sequencing multiple specialised sub-agents, with a deterministic MCP surface for host integration — is the direct architectural precedent for the OZ Policy Builder's pipeline design.
OctoLend
 — lending market with novel collateral delegation vault (~3,300 lines Rust). Audited by Runtime Verification, March 2026. All findings addressed and verified. The delegation vault's per-(borrower, market) authorization framework maps directly to the OZ Accounts Policy lifecycle (install → enforce → uninstall). Audit categories — authorization bypass, arithmetic precision, storage key scoping — are exactly the failure modes the policy synthesiser must prevent, and the deny-case harness is designed around them.
Untangled Vault
 — cross-chain yield vault, audited by Veridise, May 2025. All findings fixed or acknowledged. Audited mechanisms (share formula, multi-chain decimal precision, epoch mechanics) inform the policy synthesiser's handling of numerical parameters in generated code.
Untangled Loop
 — synthetic perpetual trading on Blend + Aquarius, live on mainnet. Source of known transaction structures for the Blend yield-claim walkthrough.
Two independent Soroban audits
 by Runtime Verification and Veridise — covering novel authorization mechanisms (delegation vaults, health factor enforcement, interest rate models). Our team is among a few with Soroban authorization mechanisms independently audited at this depth. The specific failure modes surfaced — authorization bypass, storage key scoping, arithmetic precision — are the specification for the synthesiser's correctness requirements.
Open-source, self-hosted and privacy-safe:
 All components ship MIT — MCP server, Rust synthesis library, and generated policy templates. The
@credio/policy-builder-mcp
 package runs locally or on a team's own infrastructure;
agents.crediolabs.ai
 is the hosted reference implementation, not a required dependency. No private key material passes through the MCP server; no wallet data is persisted beyond the active session — the recorder fetches only from public Soroban RPC endpoints.
Tranche 1 (Deliverable Roadmap) - MVP
$25,000 -
Tranche #1 — Foundation (weeks 1–2): Core recorder + synthesizer v1 + MCP skeleton
Deliverables:
$10,000 - Transaction recording library: ingests a Soroban transaction by hash or XDR; emits a
RecordedTransaction
 struct capturing the full sub-invocation tree, contract events, token movements, and ledger metadata. Two modes: on-chain (Soroban RPC) and simulation (local XDR).
$10,000 - Synthesizer v1: composition logic for OZ Accounts primitives (
simple_threshold
,
weighted_threshold
,
spending_limit
). Given a
RecordedTransaction
, determines
ContextRuleType
 (CallContract vs Default) and emits a
ProposedPolicy
 referencing existing OZ primitives (Path A) or flagging the need for fresh Policy trait implementation (Path B).
$5,000 - MCP server skeleton:
record_transaction
 and
synthesize_policy
 tools functional. stdio + Streamable HTTP transport.
@modelcontextprotocol/sdk
 with Zod validation. Local npm package (
@credio/policy-builder-mcp
).
OZ design review engagement: submit Phase 1 materials (synthesizer composition logic, context rule structure, Policy template skeleton) to the OZ Stellar team for feedback.
How to measure completion:
record_transaction
 accepts both hash and XDR inputs, returns a
RecordedTransaction
 JSON with source_account, contract_invocations (incl. sub-invocation tree), token_movements, and events.
synthesize_policy
 produces a deterministic
ProposedPolicy
 for ≥3 sample transaction classes: Blend
claimRewards
, SEP-41
transfer
, SoroSwap
swap_exact_tokens_for_tokens
.
MCP server builds and runs (
cargo check
 passes for Rust components;
pnpm build
 passes for TS MCP layer).
OZ design review materials submitted to OZ team
Tranche 2 (Deliverable Roadmap) - Testnet
$37,500 -
Tranche #2 — Synthesis Hardening (weeks 3–4): Path B generation + deny-case harness + agent skill
Deliverables:
$7,000 - Policy code generation (Path B): when existing OZ primitives cannot cover a required constraint dimension, synthesiser generates a fresh
Policy
 trait implementation in Rust. Mandatory
cargo check
 gate before code is presented to the user. Storage always keyed by
(smart_account, rule.id)
 — never by one alone.
$16,000 - 7-dimension deny-case harness: permit-case (original tx must pass) + deny-case generation across 7 dimensions: amount variation, asset substitution, contract substitution, function substitution, timing violations, time-window violations, policy-capacity violations. Any deny-case incorrectly permitted blocks the install flow.
$5,500 - Agent skill / Claude integration: 4-step conversational flow — intake, record, synthesize+clarify, simulate+review. Clarification targets OZ primitive config params directly:
limit_amount
 and
time_window
 for
spending_limit
;
threshold
 for
simple_threshold
.
$9,000 - Minimality checker: strip-and-verify loop — remove each constraint, run deny-cases, keep only load-bearing constraints. Ensures synthesised policy is provably minimal.
How to measure completion:
Every Path B Policy impl passes
cargo check
 against the pinned
@openzeppelin/stellar-contracts
 commit.
Deny-case harness produces correct pass/fail verdicts on ≥5 reference scenarios per walkthrough.
Claude skill clarification flow (Steps 1–4) functional end-to-end against a recorded testnet transaction.
Minimality checker passes strip-and-verify on the 3 reference policies.
Tranche 3 (Deliverable Roadmap) - Mainnet
$50,000 -
Tranche #3 — Integration, Walkthroughs & 1.0 Release (weeks 5–10): Mainnet + audit + upstream
Deliverables:
$9,500 - Freighter/all wallet integration: end-to-end install flow —
install_policy
 MCP tool produces an
UnsignedInstallTx
; Claude skill emits elicitation for user confirmation; unsigned tx handed to Freighter for user review, signing, and submission. Wallet-agnostic at MCP layer.
$500 - C-Address Tooling cohort coordination: contact the C-Address cohort via the Stellar developer forums to confirm
add_context_rule
 transaction format compatibility with at least one additional smart-account-supporting wallet beyond Freighter; publish compatibility findings in developer documentation.
$3,000 -
verify_policy
 and
install_policy
 MCP tools functional.
$6,000 - Three documented walkthroughs with live testnet receipts: (1) Blend yield-claim delegation —
spending_limit(15.3 XLM, 86400s)
; (2) SEP-41 subscription billing —
spending_limit(100 USDC, 2592000s)
; (3) Bounded SoroSwap delegation — generated slippage-cap Policy (Path B). Each: written tutorial + ≤5 min video + testnet
add_context_rule
 receipt + deny-case run output.
$1,500 - Developer documentation: MCP server setup, Claude skill install, synthesizer decision guide, how to extend for additional protocols. Published at
agents.crediolabs.ai/docs
 on Cloudflare Pages from Mintlify build.
$13,500 - Test suite: covers synthesizer on ≥10 input transaction shapes; green CI on
main
.
Independent audit of synthesizer logic + generated policy templates (scope: composition heuristics, minimality checker, deny-case generation, storage segregation,
uninstall
 cleanup). Runtime Verification preferred — Soroban-experienced, familiar with our codebase.
$3,000 - Findings remediation: Critical and High findings block 1.0 release; Medium findings remediated before release; all findings published in repo.
$1,500: OZ upstreaming PR:
frequency_limit
 primitive candidate submitted to
@openzeppelin/stellar-contracts
.
$500 - Community updates, Q&A, team support: monthly devlog posted to Stellar Developer Discord and GitHub Discussions (beginning at Tranche #0 confirmation; content: build progress, decisions taken, open questions for community input).
$2,500 - Production 1.0 release:
v1.0.0
 tagged on
main
;
@credio/policy-builder-mcp@1.0.0
 published to npm; Claude skill published to Anthropic marketplace.
$8,500 -
Ongoing maintenance commitment (post-1.0):
 Beyond the grant milestones, Credio Labs will maintain the OZ Policy Builder as a live product at agents.crediolabs.ai. This includes: monthly dependency audits (cargo audit / pnpm audit), Stellar protocol upgrade compatibility testing within one release of each testnet candidate, OZ accounts package version tracking. AI systems without maintenance infrastructure degrade — this deliverable is maintained as part of the Credio Labs platform, not discontinued after launch.
How to measure completion:
Three walkthroughs each produce a live Stellar testnet
add_context_rule
 receipt via Freighter (screenshots + ≤5 min video per walkthrough).
verify_policy
 +
install_policy
 MCP tools functional; Postman collection published.
Developer documentation live at
agents.crediolabs.ai/docs
.
All Critical and High audit findings remediated and re-reviewed.
v1.0.0
 git tag + npm registry URL for
@credio/policy-builder-mcp@1.0.0
.
Note: Detailed budget breakdown is at the bottom of the architecture doc.
Team
Credio Labs (incubated by Untangled Finance and
https://untangled.finance/
, UK, 7 years DeFi infrastructure Build). Our key people for this project are the same team that built OctoPos, OctoLend, Untangled Loop, Untangled Vault in the Stellar ecosystem:
Quan Le — Co-founder. PwC advisor to global financial institutions; in blockchain and DeFi since 2017. Master's in Applied Finance and Investment. Architect of Untangled DeFi yield infrastructure
https://www.linkedin.com/in/quan-le-binkabi/
Manrui Tang — Co-founder. PwC finance (M&A) and regulation specialist. Master's in Finance and Regulation from London School of Economics. Leads regulatory compliance AI skill development and regulator relationships; in blockchain and DeFi since 2017.
https://www.linkedin.com/in/manrui-tang-6a79aa28/
Tuan Do — Engineer & SCF on-call maintainer. Lead Developer for OctoPos- DeFi Position API and OctoGear's MPC Server
https://www.linkedin.com/in/tuanm-dev/
quntangled
```
