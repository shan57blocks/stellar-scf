Source: https://app.notion.com/p/untangledfi/OZ-Accounts-Policy-Builder-by-Crediolabs-ai-37f08dd274ad80c595a7db54f2b62cdf (public Notion page, text extracted via untangledfi.notion.site page API on 2026-09-24)

# OZ Accounts Policy Builder by [Crediolabs.ai](http://Crediolabs.ai) 

## OZ Accounts Policy Builder — Technical Architecture
Built by: Credio Labs ([https://crediolabs.ai/](https://crediolabs.ai/) incubated by Untangled Finance)
Platform: agents.crediolabs.ai
Repository: github.com/crediolabs-ai/credio-agents
License: MIT
Note: We used AI tooling to help draft this architecture document based on the RFP requirements and the following production systems: the OctoPos Transaction Builder (Stellar mainnet), the OctoLend and Untangled Vault audited Soroban contracts, the OctoGear MCP Server (EVM, 57 tools, fail-closed, production), and Agent 06 — the Tax & Portfolio MCP Server and orchestrated pipeline shipped in June 2026 for a Onchain Agents Hackathon. All technical claims map to documented, production-verified systems.
---

### Document Structure
- Part 1 — Current Architecture (what exists in production today)
- Part 2 — Gap Analysis (delta between current systems and RFP requirements)
- Part 3 — Proposed Architecture (the target design for the OZ Policy Builder)
- Part 4 — Implementation Plan (how to get from here to there, by tranche)
- Part 5 — Risk Register
---

### System Overview
```
flowchart TD
    A["User / AI Agent\n(Browser · REST · Claude Skill · MCP client)"]

    subgraph Plat["OZ Policy Builder — agents.crediolabs.ai"]
        T1["① record_transaction\nstellar_xdr · Soroban RPC fetch\n→ RecordedTransaction + FreshnessReport"]
        T2["② synthesize_policy\nPath A: configure OZ primitives\nPath B: generate Policy trait · cargo check gate"]
        T3["③ simulate_policy\n7-dimension deny-case harness\namount · asset · contract · function\ntiming · time-window · capacity"]
        T4["④ verify_policy\nMinimality checker · strip-and-verify loop\n→ MinimalPolicy"]
        T5["⑤ install_policy\n→ UnsignedInstallTx for human review"]
        T1 --> T2 --> T3 --> T4 --> T5
    end

    Fr["Freighter\nHuman reviews generated code · signs · submits"]
    St["Stellar Network\nadd_context_rule on Smart Account"]

    A --> T1
    T5 --> Fr --> St

    Fo["Foundation\nOctoPos XDR · OctoGear MCP (57 tools, fail-closed)\nAgent 06 pipeline · OctoLend + Vault (2 Soroban audits)"]
    Fo -. "XDR patterns · MCP design · deny-case spec" .-> Plat
```
---

## Part 1 — Current Architecture
The following describes four production systems that form the foundation for the OZ Policy Builder.

### 1.1 OctoPos Transaction Builder

#### What it does
The OctoPos Transaction Builder (`/txb/build`) is a production REST API that programmatically constructs Soroban transactions against Blend, SoroSwap, Phoenix, and FxDAO protocols. It deserialises Soroban XDR, builds repay/liquidate/unwind transactions, applies slippage protection, and submits to Stellar mainnet via Soroban RPC.

#### System Components
| Component | Technology | Responsibility | Status |
|---|---|---|---|
| TxB Controller | REST API (NestJS) | Exposes HTTP endpoints for transaction building | Live, mainnet |
| XDR Parser | `stellar_xdr` crate (Rust) | Deserialises Soroban transaction XDR; identifies contract invocations and token movements | Live, mainnet |
| Protocol ABIs | Contract-specific schemas | Named argument decoding for Blend, SoroSwap, Phoenix, FxDAO | Live, 7 protocols |
| Soroban Simulator | Soroban RPC | Dry-run before submission; validates transaction before signing | Live, mainnet |
| Freighter Integration | Wallet API | Signs and submits constructed transactions | Live, mainnet |

#### Data Flow — How a Transaction is Built
1. Client calls `POST /txb/build` with operation type (repay, liquidate, unwind) and address
1. TxB fetches current pool state from Blend SDK → determines required amounts
1. Constructs Soroban transaction XDR with correct function args and auth entries
1. Simulates against Soroban RPC → validates no errors, checks footprint
1. Returns unsigned transaction XDR to client
1. Client signs via Freighter → submits → confirms receipt

#### Key Infrastructure Inherited by the Policy Builder
| OctoPos TxB Component | Policy Builder Inheritance |
|---|---|
| `stellar_xdr` deserialisation | Transaction recording layer — same XDR parsing, inverted direction |
| Contract-specific ABI hints | Known function signatures for Blend, SoroSwap, SoroSwap walkthroughs |
| Soroban RPC simulation | Deny-case harness — each deny-case runs via Soroban simulation |
| Freighter integration | Install flow — `install_policy` produces `UnsignedInstallTx` handed to Freighter |
---

### 1.2 OctoGear MCP Server

#### What it does
The OctoGear MCP server (`@octogear/mcp-agent`) is a local, fail-closed AI agent runtime that enables Claude (or any MCP-compatible client) to operate a user's credit accounts across perpetual exchanges and prediction markets — Polygon (Polymarket), HyperEVM (Hyperliquid), and Hyperliquid L1. Transport: stdio. 57 tools across 3 tool kinds. The agent is fail-closed: if the bridge is unreachable, writes are refused; if any boot integrity check fails, the process exits.

#### System Components
| Component | Technology | Responsibility |
|---|---|---|
| Self-Verify Gate | Sigstore (offline), EXTCODEHASH, SHA256 | Boot-time integrity: verifies manifest sigstore signature, on-chain contract bytecodes, running `dist/index.js` artifact digest, and tool descriptor hashes. Hard-fail to stderr + `exit(1)` on any mismatch — no degraded mode. |
| GatedTransport | TypeScript stdio wrapper | 3 sequential gates on every tool call: (1) identity token `OCTOGEAR_MCP_TOKEN`, (2) client fingerprint `Claude-Desktop`, (3) pause-until-handshake. Fail-closed. |
| Tool Registry | TypeScript | 57 tools across 7 groups: `hl` (17), `gearbox-hyperevm` (12), `gearbox-polygon` (11), `portfolio` (5), `system` (5), `polymarket` (4), `util` (1). Three kinds: read (31), write (17), intent (9). |
| PolicyEnforcer | SQLite WAL (`policy_counters.db`) | Daily cap: `daily_tx_count` (default 50), `daily_notional_usd` (default $10,000). Pause state machine: NONE → WRITES_ONLY → FULL. De-escalation requires owner passkey JWT — LLM cannot self-resume. |
| IdempotencyStore | SQLite WAL (`idempotency.db`) | 5-minute UTC-bucket dedup on write tool calls — prevents double-broadcast on network retry. |
| HMAC WebSocket Bridge | Loopback-only, HMAC-SHA256 | 5 verbs: `handshake` (unpause Layer A), `revoke-signal` (global or per-surface pause), `policy-update` (owner JWT → live policy diff), `oob-request` (OS notification + user confirm for value-moving ops), `resume-request` (owner JWT → de-escalate). |
| Audit Log | Append-only JSONL (hash-chained) | Every write call logged. SHA256 hash-chaining — tampering breaks chain. Args redacted at write time. |
| OS Keyring | `@napi-rs/keyring` | K_bot and K_hl stored in OS secure enclave (macOS Keychain / Linux libsecret / Windows Credential Manager). |

#### Design Principles Inherited by the Policy Builder
| OctoGear MCP Principle | Policy Builder Application |
|---|---|
| Fail-closed by default — writes refused if bridge unreachable | `cargo check` gate: Path B policy code never presented unless it compiles against pinned OZ package. `install_policy` never auto-submits. |
| Intent-path: code/intent first, owner signs second — K_bot builds signed intent; user passkey confirms via OOB gate | `install_policy` produces `UnsignedInstallTx`; Claude skill fires elicitation before Freighter receives the tx — human reviews Rust and signs explicitly. |
| Self-verify at boot — tool descriptor hashes verified against sigstore-signed manifest | Generated template hashes are verified against the 5 audit-derived invariants before the policy is presented; `cargo check` is the compile-time equivalent of the descriptor hash check. |
| MCP is transport, not logic — 57 tools are thin wrappers over existing exchange/protocol SDKs | Synthesis engine is a pure Rust library; MCP server is a thin TypeScript wrapper with Zod-validated schemas. |
| Machine-readable error codes at every gate — structured `mcpErr` objects, no free-text | All 5 tools return structured errors; `SimulationResult` enumerates which deny-case dimensions passed or failed. |
| Per-surface granularity — `revoke-signal` pauses one exchange surface without affecting others | Policy Builder MCP: `record_transaction` and `synthesize_policy` are read/reasoning tools; `install_policy` is the only write-equivalent — gated by elicitation and Freighter sign. |
---

### 1.3 Agent 06 — Tax & Portfolio MCP Server

#### What it does
Agent 06 is an LLM-augmented onchain tax and portfolio agent: a 7-tool MCP server and 6-stage orchestrated pipeline that ingests a Celo wallet's full transaction history, classifies every transaction via rule-based logic with an LLM fallback, computes realized and unrealized PNL under three cost-basis methods (FIFO/LIFO/WAC), and exports a tax-ready CSV aligned to three jurisdiction schemas. Shipped June 2026 for the Celo Onchain Agents Hackathon. ERC-8004 registered on Celo and Ethereum.

#### System Components
| Component | Technology | Responsibility |
|---|---|---|
| MCP Server | Hand-rolled JSON-RPC 2.0 over stdio (TypeScript ESM) | Exposes 7 tools; zero `@modelcontextprotocol/sdk` runtime dependency — written to protocol spec directly after confirming a Zod compat bug in the official SDK |
| Orchestrator | `src/orchestrator/pipeline.ts` | Sequences 6 sub-agents in order: fetch → classify → price-enrich → computePnl → exportCsv → answerQuery |
| tx-classifier | Rule-based + LLM fallback (Anthropic SDK, Claude Haiku 4.5) | Classifies each transaction; unmatched transactions escalate to LLM (capped); remainder flagged for human review |
| pnl-calculator | FIFO/LIFO/WAC engine | Computes realized gains, income, yield, and gas costs per tax year |
| csv-exporter | Three schema validators | Outputs Nigeria FIRS, Kenya KRA, and OECD CARF-aligned schemas |
| nl-query | LLM sub-agent (Anthropic SDK) | Natural-language portfolio question answering after pipeline completion |

#### Architecture Patterns Inherited by the Policy Builder
| Agent 06 Pattern | Policy Builder Application |
|---|---|
| Orchestrator sequences specialised sub-agents in a fixed pipeline | record → synthesize → simulate → verify → install follows the same pipeline model — each stage owns one concern |
| Deterministic MCP surface (rule-only path, no LLM key drift between hosts) | `record_transaction` and `synthesize_policy` are deterministic; LLM called only in the clarification step |
| Rule-based primary path + LLM escalation for unclassified cases | Primitive selector: Path A (deterministic, existing OZ primitives) + LLM-assisted intent detection for Path B (novel transaction patterns) |
| Clear sub-agent ownership — each sub-agent has one responsibility, one output type | Five MCP tools have equivalent clear responsibility boundaries; no single tool does synthesis + validation + installation |
| Two entry surfaces (CLI + MCP server) sharing one pipeline core | Browser UI + REST API share the same Rust synthesis library — MCP server is a thin wrapper |
---

### 1.4 OctoLend — Audited Soroban Authorization System

#### What it does
OctoLend is a lending market with a novel collateral delegation vault — a mechanism that lets third parties supply collateral on behalf of specific borrowers, with per-(borrower, market) allocation tracking and delegation fee accrual. ~3,300 lines of production Soroban Rust. Audited by Runtime Verification Inc., March 2026, over 5 weeks.

#### Authorization Framework — Direct Analogue to OZ Policy Lifecycle
| OctoLend Mechanism | OZ Accounts Policy Analogue |
|---|---|
| `set_receiver_config` + `delegate` | `install` — sets up the authorization configuration |
| Borrow capacity check before any operation | `can_enforce` — read-only precheck before enforcement |
| Allocation enforcement | `enforce` — state-changing hook that updates quota |
| `undelegate` + storage cleanup | `uninstall` — removes all persistent state |
| Per-(borrower, market) storage key | `(smart_account, rule.id)` key — same segregation requirement |

#### Audit Findings That Directly Specify the Policy Builder
Runtime Verification's March 2026 audit surfaced findings in categories that directly constrain what a policy synthesiser must not reproduce in generated code:
| Audit Finding Category | Policy Builder Design Response |
|---|---|
| Authorization whitelist bypass | Deny-case dimension 3: contract substitution — verifies CallContract scope is enforced |
| Storage key scoping | Generated code invariant: all storage keyed by `(smart_account, rule.id)`, never by address alone |
| Arithmetic precision in state accounting | Generated spending_limit templates use integer arithmetic with explicit precision handling |
| Integer division rounding in authorization calculations | Deny-case dimension 1: amount variation — tests `amount × 1.01` to catch off-by-one rounding |
All findings were addressed and verified by Runtime Verification. The auditors' conclusion: "The Untangled team demonstrated strong responsiveness and a clear commitment to security. With the incorporated fixes, the audited codebase is materially more robust."
---

### 1.5 Untangled Vault — Second Audited Contract Set
Cross-chain yield vault implemented in Soroban following SEP-0056. Audited by Veridise Inc., May 2025. Findings covered share formula correctness, multi-chain decimal precision, epoch cancel mechanics. All findings fixed or acknowledged.
| Audit Finding Category | Policy Builder Design Response |
|---|---|
| Share formula correctness | Numerical parameter handling in generated `spending_limit` templates |
| Multi-chain decimal precision normalization | Amount-dimension testing in deny-case harness |
| Epoch mechanics | Time-window dimension in deny-case harness |
---

## Part 2 — Gap Analysis
Prior art baseline: Tyler's [kalepail/pollywallet](https://github.com/kalepail/pollywallet) demonstrates the core record-and-generate concept as a working MVP. The gap analysis below frames the delta between that MVP and the production-quality, audited, MCP-server-exposed toolkit the RFP requires. We adopt the record-and-generate workflow and XDR-based observation approach; we extend with a production synthesis engine, minimality verification, deny-case harness, Path B code generation, audit, and platform-hosted deployment. The MCP server and skill follow the Cloudflare Agent Setup pattern — tools as the structured interface, skill as the conversational entry point — with structured inputs/outputs and machine-readable error codes throughout.

### 2.1 Transaction Recording vs. Transaction Building
| Aspect | Current State (OctoPos TxB) | Gap for Policy Builder |
|---|---|---|
| Direction | Builds transactions forward from operation type | Recording layer parses backward from existing XDR |
| Input | Operation type + address | Transaction hash (Soroban RPC fetch) or XDR directly |
| Output | Unsigned transaction XDR | `RecordedTransaction` struct with full sub-invocation tree |
| Sub-invocation tree | Not needed — TxB controls what it builds | Critical — a Blend `claimRewards()` involves nested calls; policy must scope to correct level |
| Protocol coverage | Blend, SoroSwap, Phoenix, FxDAO | Same protocols + SEP-41 token contracts for subscription walkthrough |
| Unknown contracts | N/A — TxB only builds known operations | Fall back to raw `ScVal` with warning; synthesis proceeds conservatively |
Gap severity: Medium. The XDR parsing infrastructure exists. The recording layer adds: backward parsing, sub-invocation tree traversal, and a re-simulation validation step.
---

### 2.2 MCP Architecture vs. Policy Builder MCP
| Aspect | Current State (OctoGear + Agent 06 MCPs) | Gap for Policy Builder |
|---|---|---|
| Tools | 57 tools (OctoGear — perpetuals, prediction markets, credit account management: 31 read / 17 write / 9 intent); 7 tools (Agent 06 — portfolio, tax, CARF) | 5 tools (record, synthesize, simulate, verify, install) |
| Synthesis logic | None — tools call existing APIs or pipeline stages | New: policy composition engine, deny-case generation, minimality checker |
| Output type | API responses, transaction objects, tax CSVs | Generated Rust code (Path B) + config blocks (Path A) |
| Code generation gate | N/A | `cargo check` against pinned OZ package before presenting to user |
| Orchestration | Sequential sub-agent pipeline (Agent 06 — 6 stages) | Same pattern: 5-stage MCP pipeline with fixed stage ordering |
| Audit scope | OctoGear not audited; Agent 06 not audited | Synthesis logic + generated templates in scope for external audit (Tranche #3) |
Gap severity: Medium. MCP architecture, tool schema design, and elicitation patterns are proven across two production builds — OctoGear (EVM prime brokerage, 16 tools) and Agent 06 (Celo tax & portfolio, 7 tools, orchestrated pipeline). New work is the synthesis library and code generation pipeline.
---

### 2.3 OZ Policy Primitives vs. Current Authorization Patterns
| Aspect | Current State (OctoLend/OctoGear) | Gap for Policy Builder |
|---|---|---|
| Primitive selection | Hand-coded per contract | Automated: synthesiser selects from `spending_limit`, `simple_threshold`, `weighted_threshold` |
| Code generation | Written by engineers | Automated: Path B generates Policy trait implementation |
| Minimality verification | Manual review | Automated: strip-and-verify loop |
| Deny-case testing | Manual test cases | Automated: 7-dimension harness generated from recorded transaction |
| Compilation check | Manual `cargo check` in CI | Automated gate before presenting to user |
Gap severity: High — this is the primary new build. The policy synthesis engine, minimality checker, and deny-case harness do not exist yet in any form. They are the core deliverables.
---

### 2.4 Platform Deployment vs. CLI Tool
| Aspect | Typical Grant-Track Tool | This Submission |
|---|---|---|
| Access model | Install MCP server locally, configure Claude Desktop | Hosted at agents.crediolabs.ai — browser UI + REST API |
| Maintenance trigger | Grant obligation (lapses at delivery) | OctoPos and Credio agents depend on it — business continuity |
| Infrastructure | New (grant funds setup) | Existing Kubernetes stack running OctoPos in production |
| Monitoring | New (grant funds setup) | Existing Slack alerting and Kubernetes health checks |
| Dependency audits | Ad hoc | Monthly `cargo audit`/`pnpm audit` already scheduled for OctoPos |
Gap severity: Low for infrastructure; Medium for platform integration. The hosting infrastructure exists. New work is the policy builder service layer on top of it.
---

## Part 3 — Proposed Architecture

### 3.1 Architecture Overview

#### Key Design Principles
| Principle | Application |
|---|---|
| Synthesis library owns logic; MCP is transport | Rust library contains all synthesis, verification, and minimality logic. MCP server is a thin TypeScript wrapper. |
| Code-first, deploy-second | Tool produces human-readable Rust code for review. Installation is never automatic. |
| Minimal permission bias | Every generated policy is the tightest set that would permit the recorded transaction — verified by the minimality checker. |
| Deny-case-driven correctness | A policy is correct when it denies all 7 adjacent-but-wrong transaction categories — not just when it permits the original. |
| Audit lineage in generated code | Every template invariant traces back to a specific finding from an independent audit. No conventions from documentation alone. |
| Platform-maintained, not grant-and-go | Service hosted on production infrastructure with named on-call engineer and existing maintenance schedule. |

#### High-Level Component Map
```
┌──────────────────────────────────────────────────────────────┐
│                   agents.crediolabs.ai                       │
│                                                              │
│  ┌───────────-───────┐   ┌────────────────────────────────┐  │
│  │   Browser UI      │   │  REST API / MCP Server         │  │
│  │  /policy-builder  │   │  @credio/policy-builder-mcp    │  │
│  │  (review + install│   │  5 tools, Streamable HTTP/stdio│  │
│  │   via Freighter)  │   │  Zod schema validation         │  │
│  └────────┬──────────┘   └──────────────┬─────────────────┘  │
│           │                             │                    │
│           └──────────────┬──────────────┘                    │
│                          ▼                                   │
│          ┌─────────────────────────────────                  │
│          │  Synthesis Engine (Rust library)│                 │
│          │                                 │                 │
│          │  ┌────────────────────────────┐ │                 │
│          │  │  Transaction Recorder      │ │                 │
│          │  │  stellar_xdr + Soroban RPC │ │                 │
│          │  │  → RecordedTransaction     │ │                 │
│          │  └───────────┬────────────────┘ │                 │
│          │              ▼                  │                 │
│          │  ┌────────────────────────────┐ │                 │
│          │  │  Policy Synthesiser        │ │                 │
│          │  │  Path A: primitive config  │ │                 │
│          │  │  Path B: Policy trait codegen││                │
│          │  │  cargo check gate          │ │                 │
│          │  └───────────┬────────────────┘ │                 │
│          │              ▼                  │                 │
│          │  ┌────────────────────────────┐ │                 │
│          │  │  Deny-Case Harness         │ │                 │
│          │  │  7 dimensions              │ │                 │
│          │  │  Soroban simulation per case││                 │
│          │  └───────────┬────────────────┘ │                 │
│          │              ▼                  │                 │
│          │  ┌────────────────────────────┐ │                 │
│          │  │  Minimality Checker        │ │                 │
│          │  │  strip-and-verify loop     │ │                 │
│          │  └────────────────────────────┘ │                 │
│          └─────────────────────────────────┘                 │
│                          │                                   │
│                          ▼                                   │
│          ┌─────────────────────────────────┐                 │
│          │  Install Flow                   │                 │
│          │  UnsignedInstallTx → Freighter  │                 │
│          │  User review → sign → submit    │                 │
│          └─────────────────────────────────┘                 │
│                                                              │
└──────────────────────────────────────────────────────────────┘
                          │
                          ▼
             ┌──────────────────────────┐
             │   Stellar Network        │
             │   (testnet / mainnet)    │
             │   OZ Smart Account       │
             │   add_context_rule()     │
             └──────────────────────────┘
```
---

### 3.2 Transaction Recording Layer

#### Data Structures
```
pub struct RecordedTransaction {
    pub invocations: Vec<ContractInvocation>,
    pub token_movements: Vec<TokenMovement>,
    pub simulation_footprint: SorobanFootprint,
    pub source: RecordingSource, // OnChain(hash) | Simulated(xdr)
    pub ledger_metadata: LedgerMetadata,
    pub recording_freshness: RecordingFreshnessReport,
}

pub struct ContractInvocation {
    pub contract_address: Address,
    pub function_name: Symbol,
    pub args: Vec<ScVal>,
    pub sub_invocations: Vec<ContractInvocation>, // recursive
    pub auth_required: Vec<InvokerContractAuthEntry>,
}

pub struct RecordingFreshnessReport {
    pub ledger_sequence: u32,
    pub fetched_at: String,         // ISO timestamp
    pub unknown_contracts: Vec<Address>, // contracts with no ABI hint
    pub parse_confidence: f32,      // 1.0 = fully decoded; <1.0 = partial ScVal fallback
}
```
The `RecordingFreshnessReport` is included in every `record_transaction` response. It tells the user and calling agent whether any contracts were decoded without ABI hints (lower confidence), and whether the on-chain fetch succeeded. This is the policy-builder equivalent of OctoPos's `DataFreshnessReport` — every response carries explicit completeness metadata.

#### Validation Before Synthesis
Before synthesis proceeds, the recording layer re-simulates the parsed invocation against the original XDR and compares results. If the re-simulation diverges from the original transaction, synthesis is blocked with a `RECORDING_VALIDATION_FAILED` error. This prevents misparsed transactions from producing incorrect policies.
---

### 3.3 Policy Synthesis Engine

#### Synthesis Decision Tree
```
Step 1 — Determine ContextRuleType
  Single contract invoked?
    → ContextRuleType::CallContract(contract_addr)
  Multiple contracts, shared router (e.g., SoroSwap router)?
    → ContextRuleType::CallContract(router_addr) + sub-invocation note
  Admin / multi-contract pattern?
    → ContextRuleType::Default — emit BROAD_SCOPE_WARNING to user

Step 2 — Determine valid_until
  User-specified duration → convert to ledger sequence (~5s per ledger)
  No duration → AmbiguityPrompt: "How long should this delegation last?"

Step 3 — Select policy primitives (Path A first)
  Token movements observed? → spending_limit(limit_amount, time_window)
  Multiple signers required? → simple_threshold or weighted_threshold
  All constraint dimensions covered? → emit ProposedPolicy (Path A)

Step 4 — Generate new Policy trait (Path B)
  Triggered only when no primitive covers the required constraint
  → Generate Rust implementation with all template invariants
  → Run cargo check against pinned @openzeppelin/stellar-contracts
  → If check fails: surface compiler error alongside generated code; block install

Step 5 — Deny-case harness
  Run 7-dimension test suite (see 3.5)
  Any deny-case incorrectly permitted? → SYNTHESIS_ERROR; block install

Step 6 — Minimality check
  For each constraint: remove it, rerun deny-case suite
  All deny cases still pass? → constraint is redundant; remove it
  → Emit provably minimal ProposedPolicy

Step 7 — Emit ProposedPolicy + AmbiguityPrompts
  → AmbiguityPrompts fed to agent skill clarification step
  → ProposedPolicy ready for user review and install
```

#### OZ Primitive Selection Guide
| Primitive | Config parameters | Use when |
|---|---|---|
| `spending_limit` | `limit_amount`, `time_window` (seconds) | Token movements observed in recorded tx |
| `simple_threshold` | `threshold` (minimum N signatures) | Multiple signers required, equal authority |
| `weighted_threshold` | `signer_weights` map, `threshold` total weight | Hierarchical authority (admin + service key) |
| Path B generated | Per-constraint configuration | No existing primitive covers the required constraint |

#### Framework Limits Enforced
- Maximum 5 policies per context rule → synthesiser warns if approaching; blocks if exceeded
- Maximum 15 signers per context rule → validated before emit
- Maximum 15 rules per smart account → checked against on-chain state before install
- Signer set divergence caveat for threshold policies → surfaced in agent skill clarification step
---

### 3.4 Generated Policy Code (Path B)

#### Mandatory Template Invariants
All Path B outputs enforce the following invariants, each traceable to an audit finding:
Invariant 1: `require_auth` at the enforcement point
```
fn enforce(e: &Env, context: Context, authenticated_signers: Vec<Signer>,
           rule: ContextRule, smart_account: Address) {
    smart_account.require_auth(); // enforcement point, not entry point
    let key = (smart_account.clone(), rule.id);
    let mut state: PolicyState = e.storage().persistent().get(&key).unwrap_or_default();
    // update state...
    e.storage().persistent().set(&key, &state);
    e.events().publish(("policy", "enforced"), PolicyEnforced { smart_account, rule_id: rule.id });
}
```
Lineage: Authorization whitelist bypass finding, OctoLend audit, Runtime Verification March 2026.
Invariant 2: `(smart_account, rule.id)` storage key segregation
```
fn install(e: &Env, param: Self::AccountParams, rule: ContextRule, smart_account: Address) {
    smart_account.require_auth();
    // Key by BOTH smart_account AND rule.id — never by address alone.
    // Keying by address alone allows policy state from one smart account
    // to bleed into another — a storage scoping error found in OctoLend.
    let key = (smart_account.clone(), rule.id);
    e.storage().persistent().set(&key, &PolicyState::from(param));
}
```
Lineage: Storage key scoping finding, OctoLend audit, Runtime Verification March 2026.
Invariant 3: `unwrap_or` not `unwrap` on chain reads
```
fn can_enforce(e: &Env, ...) -> bool {
    let key = (smart_account.clone(), rule.id);
    // unwrap() panics if storage entry is missing — e.g. after uninstall.
    // unwrap_or_default() is safe. Identified in production Soroban code.
    let state: PolicyState = e.storage().persistent().get(&key).unwrap_or_default();
    // ...check constraint
}
```
Lineage: Production Soroban defensive coding, OctoLend and Untangled Vault build experience.
Invariant 4: `persistent()` storage for per-user data
```
// persistent() is correct for per-account policy state — it survives
// instance storage limits. instance() storage is bounded and incorrect
// for data keyed per user. Learned building OctoLend at scale.
e.storage().persistent().set(&key, &state);
```
Lineage: Soroban storage model constraint, OctoLend build experience.
Invariant 5: `uninstall` removes all storage
```
fn uninstall(e: &Env, rule: ContextRule, smart_account: Address) {
    smart_account.require_auth();
    let key = (smart_account.clone(), rule.id);
    // Remove all state — no orphaned storage entries left behind.
    e.storage().persistent().remove(&key);
}
```
Lineage: Storage cleanup requirement, OZ Policy trait specification + OctoLend audit verification.

#### Compile-Check Gate
Every Path B output runs `cargo check` against the pinned `@openzeppelin/stellar-contracts` commit in a sandboxed build environment before being displayed to the user. Code that fails the check is returned with the compiler error and marked as `COMPILE_GATE_FAILED` — the install flow is blocked.
---

### 3.5 MCP Server

#### Tool Specification
| Tool | Input | Output | Blocks on failure |
|---|---|---|---|
| `record_transaction` | `{hash?: string, xdr?: string, network: "mainnet"\|"testnet"}` | `RecordedTransaction` (incl. `RecordingFreshnessReport`) | Returns RECORDING_FAILED |
| `synthesize_policy` | `{recorded_tx: RecordedTransaction, user_responses?: AmbiguityResponse[]}` | `ProposedPolicy \| AmbiguityPrompts` | Returns SYNTHESIS_ERROR |
| `simulate_policy` | `{proposed_policy: ProposedPolicy, permit_tx: RecordedTransaction}` | `SimulationResult` (permit-case + all 7 deny-case dimensions) | Returns SIMULATION_ERROR |
| `verify_policy` | `{proposed_policy: ProposedPolicy}` | `VerificationResult` (compile-check result + minimality result) | Returns VERIFICATION_FAILED |
| `install_policy` | `{proposed_policy: ProposedPolicy, smart_account: Address}` | `UnsignedInstallTx` | Returns INSTALL_BUILD_FAILED |

#### Design Principles
| Principle | Implementation |
|---|---|
| Stateless across calls | Session state is caller's responsibility |
| Schema-first | Zod validation on all inputs; machine-readable error codes (not freeform strings) |
| No key custody | Server never holds keys, never signs, never submits |
| Freshness metadata | Every response carries `ledger_sequence` and `fetched_at` timestamps |
| Injection-safe | All inputs validated against strict Soroban type schemas before synthesis |

#### Access Modes
| Mode | Endpoint | Use case |
|---|---|---|
| Hosted (default) | `agents.crediolabs.ai/api/policy-builder` | Any developer — no installation |
| Browser UI | `agents.crediolabs.ai/policy-builder` | Interactive walkthrough, review before install |
| npm package | `@credio/policy-builder-mcp` | Local Claude Desktop or agent pipeline |
---

### 3.6 Deny-Case Harness

#### Architecture
Two test classes run against every `ProposedPolicy` before the user sees it. Any deny-case incorrectly permitted blocks the install flow with `DENY_CASE_FAILURE`.
Permit-case: Re-simulate the original recorded transaction under the generated policy. Fail → policy is too restrictive → synthesis error.
Deny-case — 7 dimensions:
| Dimension | Test | What it catches | Audit lineage |
|---|---|---|---|
| 1. Amount variation | `amount × 1.01` and `amount × 10` | spending_limit misconfiguration — off-by-one or wrong cap | Arithmetic precision findings, OctoLend audit |
| 2. Asset substitution | Substitute each asset with next most liquid Stellar asset | Asset scope bypass — policy permits XLM but also USDC | Storage key scoping findings |
| 3. Contract substitution | Substitute each CallContract target with different contract of same type | Address scope bypass — policy scoped to Blend pool A also permits pool B | Authorization whitelist findings, OctoLend audit |
| 4. Function substitution | Attempt different function on same scoped contract | Function scope bypass — policy permits claimRewards() also permits transfer() | Authorization whitelist findings |
| 5. Timing violations | Simulate at 1 ledger before and after `valid_until` | Expiry enforcement — rule does not expire as specified | Time-boundary testing, OctoLend epoch mechanics |
| 6. Time-window violations | Same tx twice within `time_window` | Rolling cap double-spend — second attempt should be denied | Spending limit enforcement, Untangled Vault audit |
| 7. Policy capacity violations | Attempt to add 6th policy to a rule (max 5) | Framework limit bypass — OZ hard limits enforced | OZ accounts framework specification |
Dimensions 3 and 4 are the highest-value checks and the most commonly underspecified in policy tooling. They are generated automatically using OctoPos's protocol adapter registry — known contract addresses and function ABIs for Blend, SoroSwap, Phoenix, and SEP-41 tokens are used to construct adjacent-but-wrong transactions without any user input.
---

### 3.7 Agent Skill / Claude Integration

#### 4-Step Interaction Flow
```
Step 1 — Intake
  User: "I want to let an agent claim my Blend yield"
  Skill: "Paste a transaction hash or XDR, or I can simulate one"

Step 2 — Record
  Calls: record_transaction
  Presents: "This called claimRewards() on Blend pool [addr],
             transferred 15.3 XLM to your wallet."
  Shows: RecordingFreshnessReport if any contracts had no ABI hint
  Confirms: "Is this the operation you want to delegate?"

Step 3 — Synthesise + Clarify
  Calls: synthesize_policy
  If AmbiguityPrompts returned, elicits on policy-relevant dimensions only:
    "This transferred 15.3 XLM. Should the policy:
     (a) cap at exactly 15.3 XLM per claim — spending_limit(15.3, per_tx)
     (b) allow up to [X] XLM over a rolling [Y]-day window
     (c) allow any amount to this specific contract?"
  → Targets: limit_amount + time_window for spending_limit;
             threshold for simple_threshold
  → Does not ask about policy-irrelevant details

Step 4 — Simulate + Review
  Calls: simulate_policy
  Presents: "✓ Original transaction permitted"
            "✓ 100× amount: denied"
            "✓ Different Blend pool: denied"
            "✓ Different asset: denied"
            "✓ Same amount twice within 24h: denied at second attempt"
  Shows generated policy code (human-readable, editable)
  Confirms: "Ready to install?" → calls install_policy → Freighter
```
---

### 3.8 Install Flow
```
1. User approves policy in browser UI or agent skill confirmation
2. install_policy tool constructs UnsignedInstallTx
   → add_context_rule(context_rule_type, name, signers, policies, valid_until)
   → transaction is unsigned — server never holds keys
3. UnsignedInstallTx → Freighter
4. Freighter displays full transaction for user review (contract, function, params)
5. User approves → Freighter signs → submits → on-chain receipt returned
```
Wallet-agnostic at MCP layer: `UnsignedInstallTx` is a format-neutral unsigned transaction. Adding a second wallet requires no changes to the synthesis engine or MCP server.
---

### 3.9 Three Walkthroughs

#### Walkthrough 1: Blend Yield-Claim Delegation
| Field | Value |
|---|---|
| Operation | `claimRewards()` on Blend pool + token transfer |
| Context rule type | `CallContract(blend_pool_addr)` — scoped to specific pool only |
| Policy (Path A) | `spending_limit(limit_amount: 20 XLM, time_window: 86400s)` |
| Clarification elicited | "Cap at 15.3 XLM or allow up to X over a rolling window?" |
| Deny cases | 200 XLM amount: denied; different Blend pool: denied; USDC instead of XLM: denied; same tx twice in 24h: denied at 2nd |
| Deliverable | Written tutorial + ≤5 min video + testnet `add_context_rule` receipt + deny-case run output |

#### Walkthrough 2: SEP-41 Subscription Billing
| Field | Value |
|---|---|
| Operation | `transfer(100 USDC)` to service provider address |
| Context rule type | `CallContract(usdc_token_contract)` |
| Policy (Path A) | `spending_limit(limit_amount: 100 USDC, time_window: 2592000s)` — 30-day rolling window |
| Clarification elicited | "Allow ±5% tolerance?" |
| Deny cases | 200 USDC: denied; different recipient address: denied (contract substitution); daily attempt (2nd within 30d): denied |
| Deliverable | Written tutorial + ≤5 min video + testnet receipt + deny-case run output |

#### Walkthrough 3: Bounded SoroSwap Delegation (Path B)
| Field | Value |
|---|---|
| Operation | `swap_exact_tokens_for_tokens()` on SoroSwap router with slippage bound |
| Context rule type | `CallContract(soroswap_router_addr)` |
| Policy (Path B) | Generated: `max_input_amount`, `min_output_amount`, `allowed_path[XLM, USDC]` — not expressible by existing primitives |
| Deny cases | Different token pair: denied; exceeded slippage bound: denied; different router: denied |
| Deliverable | Written tutorial + ≤5 min video + testnet receipt + deny-case output + Path B code + `frequency_limit` OZ upstream PR |
---

## Part 4 — Implementation Plan
Two-person core team (Rust/Soroban engineer + tooling/platform engineer), AI coding agent augmentation. Total delivery: 10 weeks from project start.
Critical path note: The independent audit is pre-scheduled with Runtime Verification before the SCF award is confirmed. This means the audit engagement begins immediately at week 5 code freeze rather than waiting for award → scheduling → start. Runtime Verification audited OctoLend and is already familiar with the codebase and Soroban patterns. This is the primary mechanism that makes a 10-week timeline viable.

### Tranche 1 — Foundation (Weeks 1–2)
Goal: Working transaction recorder + synthesiser v1 + MCP skeleton
| Deliverable | Effort | Week |
|---|---|---|
| Transaction recording library — on-chain (hash) + simulation (XDR) modes | Medium | Week 1 |
| `RecordingFreshnessReport` in every `record_transaction` response | Low | Week 1 |
| Synthesiser v1 — Path A composition logic for all 3 OZ primitives | Medium | Week 2 |
| MCP server skeleton — `record_transaction` + `synthesize_policy` functional | Low | Week 2 |
| OZ design review engagement — submit Phase 1 materials | Low | Week 2 |
Completion criteria:
- `record_transaction` accepts hash and XDR, returns `RecordedTransaction` with `RecordingFreshnessReport`
- `synthesize_policy` produces deterministic `ProposedPolicy` for ≥3 sample transaction classes: Blend `claimRewards`, SEP-41 `transfer`, SoroSwap `swap_exact_tokens_for_tokens`
- MCP server builds and runs (`cargo check` passes for Rust; `pnpm build` passes for TS layer)
- OZ design review materials submitted; acknowledgment received
- *Budget: 20% of total
---

### Tranche 2 — Synthesis Hardening (Weeks 3–4)
Goal: Path B code generation + deny-case harness + minimality checker + agent skill
| Deliverable | Effort | Week |
|---|---|---|
| Path B Policy trait generation — `cargo check` gate, template invariants enforced | High | Week 3 |
| 7-dimension deny-case harness — Soroban simulation per case | High | Week 3–4 |
| Agent skill — 4-step conversational flow with clarification targeting | Medium | Week 4 |
| Minimality checker — strip-and-verify loop | Medium | Week 4 |
| Audit PDFs committed to `references/audits/` in public repo | Low | Week 4 |
| OZ code review engagement — submit generated templates | Low | Week 4 |
Completion criteria:
- Every Path B Policy impl passes `cargo check` against pinned `@openzeppelin/stellar-contracts` commit
- Deny-case harness produces correct pass/fail verdicts on ≥5 reference scenarios per walkthrough
- Agent skill clarification flow (Steps 1–4) functional end-to-end against a recorded testnet transaction
- Minimality checker passes strip-and-verify on the 3 reference policies
- Both audit PDFs committed to `references/audits/`
- *Budget: 30% of total
---

### Tranche 3 — Integration, Walkthroughs & 1.0 Release (Weeks 5–10)
Goal: Mainnet launch + audit + upstream contribution + platform deployment
| Deliverable | Effort | Week |
|---|---|---|
| Freighter integration — `install_policy` → `UnsignedInstallTx` → Freighter sign → submit | Medium | Week 5 |
| `verify_policy` + `install_policy` MCP tools functional | Low | Week 5 |
| Browser UI at agents.crediolabs.ai/policy-builder | Medium | Week 5–6 |
| REST API at agents.crediolabs.ai/api/policy-builder | Low | Week 5 |
| Walkthrough 1: Blend yield-claim (tutorial + ≤5 min video + testnet receipt + deny-case output) | Medium | Week 6 |
| Walkthrough 2: SEP-41 subscription billing | Medium | Week 6 |
| Walkthrough 3: Bounded SoroSwap (Path B + deny-case output + `frequency_limit` PR) | High | Week 6–7 |
| Developer documentation at agents.crediolabs.ai/docs | Medium | Week 7 |
| Test suite — ≥10 input transaction shapes, green CI on `main` | Medium | Week 7 |
| Code freeze + audit start (pre-scheduled with Runtime Verification) | — | Week 5 |
| Independent audit — synthesiser logic + generated templates (Runtime Verification) | External | Weeks 5–8 |
| Findings remediation + re-review | High (if findings) | Weeks 9–10 |
| OZ upstream PR — `frequency_limit` primitive candidate | Low | Week 9 |
| Production 1.0 release — `v1.0.0` tag + `@credio/policy-builder-mcp@1.0.0` npm | Low | Week 10 |
Note on parallel audit track: The audit runs in parallel with walkthroughs and documentation (weeks 5–8). Code freeze applies to the synthesiser and generated templates only — integration work, UI, and documentation continue in parallel.
Completion criteria:
- Three walkthroughs each produce a live Stellar testnet `add_context_rule` receipt via Freighter
- `verify_policy` + `install_policy` MCP tools functional; Postman collection published
- Developer documentation live at `agents.crediolabs.ai/docs`
- Browser UI and REST API live at `agents.crediolabs.ai/policy-builder`
- Independent audit PDF published to `references/audits/`
- All Critical and High findings remediated and re-reviewed
- `v1.0.0` git tag + npm registry URL for `@credio/policy-builder-mcp@1.0.0`
- OZ upstream PR for `frequency_limit` opened
- *Budget: 40% of total
---

### Requirements → Deliverables Mapping
| RFP Requirement | Tranche(s) | Deliverable |
|---|---|---|
| Transaction recording/observation layer | T1 | Recording library — on-chain + simulation modes |
| Context rule + policy synthesiser | T1–T2 | Synthesiser v1 (T1) + Path B codegen + minimality checker (T2) |
| Generated policy code in Rust/Soroban | T2 | Path B + `cargo check` gate + all 5 template invariants |
| MCP server (4 tools minimum) | T1–T3 | 5 tools across T1–T3 |
| Agent skill | T2 | 4-step conversational flow with clarification targeting |
| Simulation/dry-run harness | T2 | 7-dimension deny-case harness |
| Wallet integration | T3 | Freighter end-to-end install flow |
| Three walkthroughs with documentation | T3 | Blend, SEP-41, SoroSwap — tutorial + video + testnet receipt |
| Platform access (hosted service) | T3 | Browser UI + REST API at agents.crediolabs.ai |
| Open source, permissive license | All | MIT — github.com/crediolabs-ai/credio-agents |
---

## Part 5 — Risk Register
| Risk | Likelihood | Impact | Mitigation |
|---|---|---|---|
| OZ accounts package changes during build | Medium | High — generated templates may not compile against new version | Pin to specific commit for audit and 1.0 release. Document version compatibility matrix. Compatibility test on each OZ package update post-launch. |
| Soroban XDR format shift breaks recording layer | Medium | High — `record_transaction` fails silently or produces wrong output | Version recording layer against Soroban protocol version. `RecordingFreshnessReport` includes `parse_confidence` — callers know if any contracts decoded without ABI hint. Integration tests on each Stellar protocol upgrade. |
| Audit scheduling delays Tranche 3 | Low | High — 1.0 release pushed on critical path | Audit pre-scheduled before SCF award is confirmed, if possible. Engagement begins at week 5 code freeze with no scheduling lag. Audit scope is narrower than OctoLend (synthesiser + templates only, not full codebase). |
| Deny-case generation is insufficient for a novel Path B policy | Low-Medium | High — policy appears correct but is over-permissive | Deny-case generation is deterministic and published. Users can add custom deny-cases. External audit scope expliciFtly includes deny-case generation correctness. |
| Freighter does not support `add_context_rule` transaction format | Low | High — install flow blocked entirely | Confirm with Freighter team before Tranche 3 begins (end of Tranche 1). Design `UnsignedInstallTx` as wallet-agnostic so second wallet can be added without rework. |
| Path B code generation produces code that compiles but is semantically incorrect | Low | High — policy permits unintended transactions | External audit scope explicitly includes all Path B templates shipped in 1.0. Auditor asked specifically: "Does generated `uninstall` clean up all state?" |
| OctoPos infrastructure downtime affects policy builder platform | Low | Medium — hosted service unavailable | Same Kubernetes stack, same SLA, same Slack alerting as OctoPos mainnet. Not new infrastructure. |
| Low developer adoption of OZ accounts framework | Low | Low — policy builder has fewer users than projected | Tool is MIT-licensed and platform-hosted; low-friction access reduces adoption barrier. OctoPos and Credio agents serve as production users regardless. |
---

## Budget Breakdown by Sub-Deliverable
Total request: $125,000
Total hours: 1,250 hours = T0 kickoff (125 hrs, no breakdown) + 1,125 hrs across T1–T3 (10 build weeks + 1 calendar year of post-1.0 maintenance)
---

### Payment Schedule
| Milestone | % | Amount |
|---|---|---|
| Project start (kickoff) | 10% | $12,500 |
| Tranche 1 completion | 20% | $25,000 |
| Tranche 2 completion | 30% | $37,500 |
| Tranche 3 completion | 40% | $50,000 |
| Total | 100% | $125,000 |
---

### Allocation philosophy
Agentic development compresses implementation time but not correctness work. The synthesiser's core claim, that a generated policy is minimal and denies the right adjacent transactions, cannot be verified by running the code once. It requires a purpose-built adversarial harness, a strip-and-verify loop, and ongoing infrastructure that keeps the tool correct as Soroban evolves.
The budget reflects this: design, testing & hardening, infrastructure, and maintenance together account for 69% of total spend; software development and documentation account for 22%; T0 kickoff is 10%.
Maintenance is structural to the product: AI-generated Soroban policies that are not retested against each Stellar protocol upgrade become a liability. The ongoing maintenance commitment covers monthly dependency sweeps, upgrade compatibility testing, and OZ package version tracking.
| Category | Total hours | % | Cost |
|---|---|---|---|
| T0 — Kickoff | 125 hrs | 10% | $12,500 |
| Design & architecture | 237 hrs | 19% | $23,700 |
| Software development | 258 hrs | 21% | $25,800 |
| Testing & hardening | 485 hrs | 39% | $48,500 |
| Infrastructure & maintenance | 135 hrs | 11% | $13,500 |
| Documentation & community | 10 hrs | 1% | $1,000 |
| Total | 1,250 hrs | 100% | $125,000 |
---

### Tranche 1: Foundation (Weeks 1–2) — $25,000 / 250 hours
Core recorder + synthesizer v1 + MCP skeleton + OZ design review submission. Design-heavy because the `RecordedTransaction` schema and synthesizer data model propagate into every downstream component. Getting them wrong means reworking the harness and the minimality checker in Tranche 2.

#### D1.1 Transaction recording library — 100 hrs / $10,000
`RecordedTransaction` struct ingesting a Soroban transaction by hash or XDR; full sub-invocation tree, contract events, token movements, ledger metadata. Two modes: on-chain (Soroban RPC) and simulation (local XDR).
| Sub-task | Hours | Cost |
|---|---|---|
| XDR parser architecture (source account, sub-invocation tree, contract events, token movements, ledger metadata; on-chain vs. simulation mode branching) | 30 | $3,000 |
| Transaction recording library implementation (on-chain path: Soroban RPC; simulation path: local XDR) | 45 | $4,500 |
| Unit + integration tests, recorder: 3 target transaction classes (Blend `claimRewards`, SEP-41 `transfer`, SoroSwap `swap_exact_tokens_for_tokens`); both input modes (hash + XDR) | 25 | $2,500 |
| Subtotal | 100 | $10,000 |

#### D1.2 Synthesizer v1 — 100 hrs / $10,000
Composition logic for OZ Accounts primitives (`simple_threshold`, `weighted_threshold`, `spending_limit`). Given a `RecordedTransaction`, determines `ContextRuleType` (CallContract vs Default) and emits a `ProposedPolicy` referencing existing OZ primitives (Path A) or flagging the need for fresh Policy trait implementation (Path B).
| Sub-task | Hours | Cost |
|---|---|---|
| Synthesizer v1 composition logic design (`ContextRuleType` decision tree, `ProposedPolicy` schema, OZ primitive mapping table) | 32 | $3,200 |
| Synthesizer v1 implementation, Path A only (existing OZ primitives); deterministic `ProposedPolicy` output | 43 | $4,300 |
| Unit + integration tests, synthesizer v1: determinism on 3 sample inputs; `ContextRuleType` classification accuracy; `ProposedPolicy` schema conformance | 25 | $2,500 |
| Subtotal | 100 | $10,000 |

#### D1.3 MCP server skeleton — 50 hrs / $5,000
`record_transaction` and `synthesize_policy` tools functional. stdio + Streamable HTTP transport. `@modelcontextprotocol/sdk` with Zod validation. Local npm package (`@credio/policy-builder-mcp`).
| Sub-task | Hours | Cost |
|---|---|---|
| MCP server tool surface design: 5 tool specs (`record_transaction`, `synthesize_policy`, `simulate_policy`, `verify_policy`, `install_policy`), Zod schemas, stdio + Streamable HTTP transport decisions | 25 | $2,500 |
| Infrastructure setup: repo scaffold, CI pipeline (GitHub Actions), Soroban RPC integration, local XDR simulation mode, `pnpm` + `cargo` workspace config | 25 | $2,500 |
| Subtotal | 50 | $5,000 |

#### D1.4 OZ design review engagement — 0 hrs / $0
The OZ design review is a milestone event, not a build workstream. The Phase 1 submission materials ARE the design artefacts from D1.1, D1.2, D1.3 (90 design hours already allocated above). The review itself is a submission + acknowledgment cycle that does not consume additional build hours.
T1 total: 250 hrs / $25,000
---

### Tranche 2: Synthesis Hardening (Weeks 3–4) — $37,500 / 375 hours
Path B generation + 7-dimension deny-case harness + minimality checker + agent skill. This is the hardening-dominant tranche. The deny-case harness and minimality checker are the correctness argument for the synthesiser, not features but proofs. Half the tranche budget is testing and validation work.

#### D2.1 Policy code generation (Path B) — 70 hrs / $7,000
When existing OZ primitives cannot cover a required constraint dimension, synthesiser generates a fresh `Policy` trait implementation in Rust. Mandatory `cargo check` gate before code is presented to the user. Storage always keyed by `(smart_account, rule.id)`, never by one alone.
| Sub-task | Hours | Cost |
|---|---|---|
| Path B code generation implementation: Rust Policy trait template engine, `cargo check` gate, storage key invariant, Path B trigger detection heuristic | 45 | $4,500 |
| Path B generated policy review + correction cycles: generated Rust code review against OZ contract primitives; storage key scoping verification; correction rounds until all `cargo check` gates green | 25 | $2,500 |
| Subtotal | 70 | $7,000 |

#### D2.2 7-dimension deny-case harness — 160 hrs / $16,000
Permit-case (original tx must pass) + deny-case generation across 7 dimensions: amount variation, asset substitution, contract substitution, function substitution, timing violations, time-window violations, policy-capacity violations. Any deny-case incorrectly permitted blocks the install flow.
| Sub-task | Hours | Cost |
|---|---|---|
| Deny-case harness specification: 7-dimension failure mode catalogue, derived from Runtime Verification (OctoLend) and Veridise (Untangled Vault) audit findings | 50 | $5,000 |
| 7-dimension deny-case harness implementation: 7 test generators, permit-case runner, deny-case runner, structured output format | 40 | $4,000 |
| Deny-case harness validation: ≥5 reference scenarios per walkthrough (3 walkthroughs = ≥15 scenarios total); boundary condition tests; adversarial inputs probing each of the 7 dimensions | 70 | $7,000 |
| Subtotal | 160 | $16,000 |

#### D2.3 Agent skill / Claude integration — 55 hrs / $5,500
4-step conversational flow (intake, record, synthesize+clarify, simulate+review). Clarification targets OZ primitive config params directly: `limit_amount` and `time_window` for `spending_limit`; `threshold` for `simple_threshold`.
| Sub-task | Hours | Cost |
|---|---|---|
| Agent skill conversation flow design: 4-step flow spec, elicitation targets per OZ primitive, error state handling | 20 | $2,000 |
| Agent skill implementation: 4-step Claude conversational flow, primitive param elicitation, `synthesize_policy` and `simulate_policy` tool calls from within skill | 15 | $1,500 |
| Agent skill hardening: error state testing (RPC failure, malformed XDR, ambiguous policy, Freighter not available); session recovery; adversarial inputs; regression against synthesizer v1 baseline | 20 | $2,000 |
| Subtotal | 55 | $5,500 |

#### D2.4 Minimality checker — 90 hrs / $9,000
Strip-and-verify loop: remove each constraint, rerun deny-cases, keep only load-bearing constraints. Ensures synthesised policy is provably minimal.
| Sub-task | Hours | Cost |
|---|---|---|
| Minimality checker algorithm design: constraint dependency graph model, strip-and-verify protocol, termination conditions, handling of interdependent constraints | 30 | $3,000 |
| Minimality checker testing: strip-and-verify on 3 reference policies; false-positive analysis; false-negative analysis; edge cases with interdependent constraints | 60 | $6,000 |
| Subtotal | 90 | $9,000 |
T2 total: 375 hrs / $37,500
---

### Tranche 3: Integration, Walkthroughs, 1.0 Release & Maintenance (Weeks 5–10) — $50,000 / 500 hours
Freighter integration, three documented walkthroughs, full test suite, production 1.0 release, and ongoing maintenance infrastructure that sustains the platform post-release. Testing & hardening is the dominant category; infrastructure and maintenance is the second-largest.

#### D3.1 Freighter integration — 95 hrs / $9,500
End-to-end install flow. `install_policy` MCP tool produces an `UnsignedInstallTx`; Claude skill emits elicitation for user confirmation; unsigned tx handed to Freighter for user review, signing, and submission. Wallet-agnostic at MCP layer.
| Sub-task | Hours | Cost |
|---|---|---|
| Freighter integration design: `UnsignedInstallTx` payload spec, elicitation protocol (MCP tool → Claude skill → Freighter), wallet-agnostic abstraction layer | 20 | $2,000 |
| Freighter integration implementation: wallet signing flow (unsigned tx → Freighter review → user sign → submit), elicitation → sign → submit pipeline | 25 | $2,500 |
| Freighter integration testing: live Stellar testnet `add_context_rule` receipts for all 3 walkthroughs; Freighter signing validation; C-Address cohort compatibility check (≥1 additional wallet confirmed) | 50 | $5,000 |
| Subtotal | 95 | $9,500 |

#### D3.2 C-Address Tooling cohort coordination — 5 hrs / $500
Contact the C-Address cohort via the Stellar developer forums to confirm `add_context_rule` transaction format compatibility with at least one additional smart-account-supporting wallet beyond Freighter; publish compatibility findings in developer documentation.
| Sub-task | Hours | Cost |
|---|---|---|
| C-Address cohort coordination + wallet compatibility findings publication | 5 | $500 |
| Subtotal | 5 | $500 |

#### D3.3 `verify_policy` + `install_policy` MCP tools — 30 hrs / $3,000
Tool schemas, Zod validation, elicitation-before-action design, `UnsignedInstallTx` output.
| Sub-task | Hours | Cost |
|---|---|---|
| `verify_policy` + `install_policy` MCP tools implementation: tool schemas, Zod validation, elicitation-before-action design, `UnsignedInstallTx` output | 30 | $3,000 |
| Subtotal | 30 | $3,000 |

#### D3.4 Three documented walkthroughs — 60 hrs / $6,000
(1) Blend yield-claim delegation: `spending_limit(15.3 XLM, 86400s)`; (2) SEP-41 subscription billing: `spending_limit(100 USDC, 2592000s)`; (3) Bounded SoroSwap delegation: generated slippage-cap Policy (Path B). Each walkthrough produces a live Stellar testnet `add_context_rule` receipt via Freighter, deny-case run outputs, and a Postman collection. Tutorials + ≤5 min videos are produced inline as part of the test execution work.
| Sub-task | Hours | Cost |
|---|---|---|
| End-to-end walkthrough testing: live testnet receipts validated (3 scenarios); deny-case run outputs attached; Postman collection; tutorial + ≤5 min video produced inline | 60 | $6,000 |
| Subtotal | 60 | $6,000 |

#### D3.5 Developer documentation — 15 hrs / $1,500
MCP server setup, Claude skill install, synthesizer decision guide. Published at `agents.crediolabs.ai/docs` on Cloudflare Pages from Mintlify build. Scope is narrowed to setup + install + decision guide only; the protocol extension guide is deferred to post-grant.
| Sub-task | Hours | Cost |
|---|---|---|
| Cloudflare Pages + Mintlify docs pipeline: agents.crediolabs.ai/docs build + deploy from `main`; CI integration | 15 | $1,500 |
| Subtotal | 15 | $1,500 |

#### D3.6 Test suite — 135 hrs / $13,500
Covers synthesizer on ≥10 input transaction shapes; green CI on `main`. Includes end-to-end regression and performance benchmarks.
| Sub-task | Hours | Cost |
|---|---|---|
| Test suite architecture + walkthrough experience design: ≥10 input shapes, CI coverage targets, regression baselines from T1+T2; 3 scenario specs, tutorial structure, ≤5 min video outline | 15 | $1,500 |
| Test suite implementation + CI hardening: ≥10 synthesizer input shapes; green CI on `main`; deny-case suite regression; minimality checker regression; `cargo check` gate regression across all Path B templates | 70 | $7,000 |
| Post-release regression + performance testing: response time benchmarks under concurrent tool calls; edge-case inputs from community usage; systematic regression against all known-good scenarios | 50 | $5,000 |
| Subtotal | 135 | $13,500 |

#### D3.7 Independent audit — 30 hrs / $3,000
Audit fee paid separately from the audit bank, not from this grant. The 30 hours covers Credio Labs team time to work with the audit team (kickoff, regular status meetings, triaging findings, fixing reported bugs, re-verification rounds).
| Sub-task | Hours | Cost |
|---|---|---|
| Audit team collaboration: audit kickoff sessions, regular status meetings with auditors, triaging findings, fixing reported bugs, re-verification rounds | 30 | $3,000 |
| Subtotal | 30 | $3,000 |

#### D3.8 Findings remediation — 0 hrs / $0 (covered by D3.7)
Critical and High findings block 1.0 release; Medium findings remediated before release; all findings published in repo. The team time to address findings is included in D3.7.

#### D3.9 OZ upstreaming PR — 15 hrs / $1,500
`frequency_limit` primitive candidate submitted to `@openzeppelin/stellar-contracts`; review cycle participation.
| Sub-task | Hours | Cost |
|---|---|---|
| OZ upstreaming PR: `frequency_limit` primitive candidate submitted to `@openzeppelin/stellar-contracts`; review cycle participation | 15 | $1,500 |
| Subtotal | 15 | $1,500 |

#### D3.10 Community updates — 5 hrs / $500
Monthly devlog posted to Stellar Developer Discord and GitHub Discussions (build progress, decisions taken, open questions for community input).
| Sub-task | Hours | Cost |
|---|---|---|
| Community devlog: monthly posts to Stellar Developer Discord + GitHub Discussions (build progress, decisions, open questions, beginning at T1 confirmation) | 5 | $500 |
| Subtotal | 5 | $500 |

#### D3.11 Production 1.0 release — 25 hrs / $2,500
`v1.0.0` tagged on `main`; `@credio/policy-builder-mcp@1.0.0` published to npm; Claude skill published to Anthropic marketplace; production deployment to agents.crediolabs.ai.
| Sub-task | Hours | Cost |
|---|---|---|
| Kubernetes deployment + monitoring: agents.crediolabs.ai service configuration, alerting setup, health check endpoints | 15 | $1,500 |
| npm + Claude marketplace publish + v1.0.0 release: `@credio/policy-builder-mcp@1.0.0` on npm; Claude skill submitted to Anthropic marketplace; `v1.0.0` git tag | 10 | $1,000 |
| Subtotal | 25 | $2,500 |

#### D3.12 Ongoing maintenance — 85 hrs / $8,500
1 calendar year post-1.0 maintenance commitment: monthly dependency audits, Stellar protocol upgrade compatibility testing, OZ accounts package version tracking.
| Sub-task | Hours | Cost |
|---|---|---|
| Maintenance operations design: runbook for monthly dependency sweep protocol, Stellar protocol upgrade compatibility test process, OZ package version tracking workflow | 15 | $1,500 |
| Ongoing maintenance, monthly `cargo audit` / `pnpm audit` dependency sweeps (1 calendar year post-release, ~2 hrs/month): vulnerability triage, patch application, re-verification against deny-case suite | 25 | $2,500 |
| Stellar protocol upgrade compatibility testing (1 calendar year): synthesizer, recorder, and MCP server tested within one release of each testnet candidate; breaking changes patched and re-released | 35 | $3,500 |
| OZ accounts package version tracking + compatibility updates (1 calendar year, light-touch monthly check): `@openzeppelin/stellar-contracts` version tracking; generated policy template updates when OZ primitives change | 10 | $1,000 |
| Subtotal | 85 | $8,500 |
T3 total: 500 hrs / $50,000
---

### Budget Summary
| Tranche | % | Weeks | Hours | Budget |
|---|---|---|---|---|
| T0 — Kickoff | 10% | (pre-T1) | 125 hrs | $12,500 |
| Tranche 1: Foundation | 20% | 1–2 | 250 hrs | $25,000 |
| Tranche 2: Synthesis Hardening | 30% | 3–4 | 375 hrs | $37,500 |
| Tranche 3: Integration, Release & Maintenance | 40% | 5–10 (build) + 1 calendar year maintenance | 500 hrs | $50,000 |
| Total | 100% | 10 build weeks + 1 calendar year maintenance | 1,250 hrs | $125,000 |

#### Spend by category
| Category | Hours | Cost | % |
|---|---|---|---|
| T0 — Kickoff | 125 hrs | $12,500 | 10% |
| Design & architecture | 237 hrs | $23,700 | 19% |
| Software development | 258 hrs | $25,800 | 21% |
| Testing & hardening | 485 hrs | $48,500 | 39% |
| Infrastructure & maintenance | 135 hrs | $13,500 | 11% |
| Documentation & community | 10 hrs | $1,000 | 1% |
| Total | 1,250 hrs | $125,000 | 100% |

## Appendix: Credential Mapping
Every technical claim in this document traces to a specific prior deliverable:
| Prior work | Section it informs |
|---|---|
| OctoPos Transaction Builder — `stellar_xdr` parsing, Soroban RPC simulation, Freighter integration | 3.2 (Recording layer), 3.8 (Install flow) |
| OctoPos Protocol Adapters (Blend, SoroSwap, Phoenix, FxDAO) | 3.5 (Deny-case harness — known contract ABIs used for adjacent transactions) |
| OctoGear MCP Server — 57 tools (read/write/intent kinds), fail-closed trust model, self-verify gate (sigstore + bytecode + tool descriptor hash), intent-path pattern (signed intent → owner passkey confirm), HMAC bridge, dual pause layers | 1.2 (full component table), 3.1 (fail-closed + code-first principles), 3.5 (MCP server design) |
| Agent 06 (Celo Tax & Portfolio) — orchestrated sub-agent pipeline, deterministic MCP surface, rule+LLM hybrid classification, hand-rolled stdio transport (June 2026) | 1.3 (architecture patterns), 3.1 (key design principles), 2.2 (MCP gap analysis) |
| OctoLend — collateral delegation vault (authorization lifecycle, storage segregation) | 3.3 (Synthesis decision tree), 3.4 (Path B invariants 1–3) |
| OctoLend audit, Runtime Verification March 2026 — authorization bypass, storage key scoping, arithmetic precision | 3.6 (Deny-case dimensions 1–4, audit lineage column) |
| Untangled Vault — share formula, epoch mechanics, decimal precision | 3.4 (Path B invariant 3), 3.6 (deny-case dimensions 6) |
| Untangled Vault audit, Veridise May 2025 — share formula correctness, epoch mechanics | 3.4 (Path B invariants), 3.6 (deny-case dimension 6) |
