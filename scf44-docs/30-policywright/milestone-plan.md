Source: https://drive.google.com/file/d/1keCLSG-megDf7iLDd73fGwUuDGpwLXYq/view (Policywright-Milestone-Plan.docx)

Policywright — Milestone & Deliverable Plan
SCF #44 · RFP Track · “OZ accounts policy builder” · XXIX Labs · License: MIT
This plan expands the three SCF Build tranches into verifiable deliverables, each with a success criterion a reviewer can test, the verification method, the Stellar/Soroban components it touches, dependencies, and budget. It is a companion to the Technical Architecture document and the SCF Build submission form.
Scope of this plan. Policywright is an MCP server, a Claude skill, and a policy synthesizer that turns a recorded (or simulated) Soroban transaction into the least-privilege OpenZeppelin smart-account authorization — a context rule plus the minimal policies — that permits exactly that flow. Every deliverable below is a Stellar/Soroban artifact; nothing here is generic tooling.
Timeline

Policywright tranche timeline: kickoff, MVP (15 Aug), testnet (30 Sep), mainnet (15 Nov)
Assumptions. Execution is solo (single engineer, AI-augmented workflow), sized for ~4 months. Dates assume a mid-July kickoff after award acceptance and KYC/KYB; if the award lands later, all dates shift uniformly. The payment schedule is fixed by SCF: 10% on approval, then 20% / 30% / 40% on tranche completion. The security audit is provided at Tranche 3 completion via the Audit Bank and is not included in the budget. Total request: $55,000 in XLM.
Definition of “done” (applies to every deliverable)
A deliverable is complete only when it is (a) clear — obvious what was shipped; (b) measurable — a concrete success signal; (c) verifiable — a reviewer can test, view, or validate it; and (d) outcome-based. Wherever a deliverable involves on-chain behavior, completion is evidenced by a testnet or mainnet contract ID and transaction links, plus a short recorded demo. Code deliverables are evidenced by a public commit or release with green CI.

Tranche 1 — MVP (testnet) · funded by 10% + 20% = $16,500 · complete 15/08/2026
Tranche 1 is real development, not planning: the architecture is locked before kickoff, so T1 ships a working record → synthesize → compile → testnet-deploy path.
D1.1 — Hardened recording / observation layer · $5,000
	•	Description. Two adapters yielding a normalized RecordedTx: a live path that queries Soroban RPC getTransaction(hash) and decodes the transaction envelope (the InvokeHostFunction operation → invokeContract: contract address, function name, ScVal arguments) and contract events from the result meta (SEP-41 / Stellar Asset Contract transfer events → token movements), and a simulated path via simulateTransaction.
	•	Success criterion. The CLI ingests a real testnet Blend yield-claim → Soroswap swap-to-USDC transaction by hash and emits a structured RecordedTx (contracts, functions, decoded args, token movements).
	•	Verification. Reviewer runs the CLI against the provided testnet hash and inspects the JSON output; demo recorded.
	•	Stellar components. Soroban RPC (getTransaction, simulateTransaction), XDR/ScVal decoding (scValToNative), SEP-41/SAC events.
	•	Dependencies. A representative testnet transaction (generated first-party from a Blend claim flow).
D1.2 — Least-privilege synthesizer (scope + composed policies) · $6,000
	•	Description. Converts a RecordedTx into a context rule scoped to exactly the observed (contract, function) pairs, with gross-outflow spend caps (assets only received get no cap), lifetime, and frequency bounds; composes a stock OZ spending_limit.
	•	Success criterion. From the recorded transaction, emits context-rule.json plus composed spending_limit parameters; unit tests for scope binding, the gross-vs-net cap rule, and the inflow-only case pass in CI.
	•	Verification. Reviewer reads the emitted spec and runs the test suite (green in CI).
	•	Stellar components. OZ accounts context rules + policies; spending_limit primitive.
	•	Dependencies. D1.1.
D1.3 — Policy compilation + testnet deployment · $3,500
	•	Description. A generated Soroban Policy contract compiles via stellar-cli and deploys to testnet.
	•	Success criterion. The generated policy compiles with stellar contract build and is deployed to testnet; contract ID shared.
	•	Verification. Reviewer views the testnet contract ID on an explorer; build reproducible from the repo.
	•	Stellar components. soroban-sdk, stellar-cli (build, deploy), testnet.
	•	Dependencies. D1.2.
D1.4 — Open-source CLI, repo, and CI · $2,000
	•	Description. Public MIT repository with the v0 spike extended into a robust CLI, README, and CI (typecheck + tests + demo).
	•	Success criterion. Public repo with green CI; npm run demo reproduces the record → synthesize → dry-run artifacts.
	•	Verification. Reviewer opens the repo, sees the passing CI badge, and runs the demo.
	•	Stellar components. N/A (delivery infrastructure for the Stellar tooling above).
	•	Dependencies. D1.1–D1.3.

Tranche 2 — Testnet expansion · funded by 30% = $16,500 · complete 30/09/2026
Tranche 2 turns the engine into agent-usable tooling and closes the loop to a real testnet smart account.
D2.1 — MCP server · $4,500
	•	Description. Exposes record, synthesize, simulate, and verify over the Model Context Protocol with structured I/O, deterministic behavior, and machine-readable error codes.
	•	Success criterion. The server runs locally and an agent calls each tool end to end; a reference session is recorded.
	•	Verification. Reviewer runs the MCP server and the recorded agent session; tool schemas documented.
	•	Stellar components. Wraps the Soroban recording/synthesis/simulation capabilities for agents.
	•	Dependencies. T1.
D2.2 — Claude skill · $3,000
	•	Description. A conversational wrapper over the MCP server that asks for clarification when scope is ambiguous (e.g. cap at the observed amount vs. a weekly allowance).
	•	Success criterion. Skill packaged; a demo shows “grant permission to do X from this transaction” producing a reviewed policy.
	•	Verification. Reviewer installs the skill and runs the demo prompt.
	•	Stellar components. Conversational entry point to Soroban policy generation.
	•	Dependencies. D2.1.
D2.3 — Dry-run harness + argument-level scope · $3,500
	•	Description. Permit-case plus auto-generated deny-cases (different asset, larger amount, out-of-window, repeated call) evaluated against the OZ policy lifecycle, plus configurable argument constraints (e.g. swap path) that close the v0 scope gap.
	•	Success criterion. The harness outputs a permit/deny/flag report for a generated policy including an argument-constrained case (BLND→XLM denied when enabled); tests green.
	•	Verification. Reviewer runs the harness and reads the report; tests in CI.
	•	Stellar components. Soroban simulateTransaction + a policy-semantics evaluator.
	•	Dependencies. D1.2.
D2.4 — Net-new policy codegen with storage segregation · $2,500
	•	Description. Generates stateful Policy contracts with storage keyed by (smart_account, context_rule_id); compose-first / generate-second mode.
	•	Success criterion. Generates both a composed-policy configuration and a net-new stateful policy contract; both compile and pass simulation.
	•	Verification. Reviewer compiles the generated contract and runs it through the harness.
	•	Stellar components. OZ Policy trait lifecycle (install/can_enforce/enforce/uninstall); soroban-sdk persistent storage.
	•	Dependencies. D1.3, D2.3.
D2.5 — Wallet integration (testnet, end-to-end) · $3,000
	•	Description. Record → generate → simulate → sign → install on a testnet Soroban smart account via a C-Address Tooling cohort wallet, signed client-side (keys never leave the client).
	•	Success criterion. A testnet smart account with an installed generated policy; end-to-end demo recorded.
	•	Verification. Reviewer views the testnet smart account and the installed policy (contract IDs + tx links).
	•	Stellar components. C-addresses, stellar-wallets-kit, OZ accounts install flow.
	•	Dependencies. D2.4; wallet coordination (see Ecosystem Coordination Plan).

Tranche 3 — Mainnet launch · funded by 40% = $22,000 · complete 15/11/2026
Tranche 3 is production: documented walkthroughs, OZ-validated generated code, a versioned release, a mainnet demonstration, and audit readiness.
D3.1 — Three documented end-to-end walkthroughs · $5,000
	•	Description. Blend yield-claim, SEP-41 subscription, and bounded Soroswap delegation (or equivalents), each runnable end to end.
	•	Success criterion. Three published walkthroughs, each reproducible; recorded.
	•	Verification. Reviewer follows a walkthrough to a working result.
	•	Stellar components. Blend, SEP-41 tokens, Soroswap; OZ policies for each.
	•	Dependencies. T2.
D3.2 — OpenZeppelin validation + coordination · $3,000
	•	Description. OZ accounts maintainers review generated-code quality as technical reviewers; identify primitives worth upstreaming.
	•	Success criterion. OZ review notes incorporated; a short summary of validated templates and upstream proposals.
	•	Verification. Reviewer reads the incorporated-feedback summary.
	•	Stellar components. OZ accounts package; generated Soroban Policy contracts.
	•	Dependencies. D2.4; OZ coordination (see Ecosystem Coordination Plan).
D3.3 — Production release · $5,500
	•	Description. A versioned MCP server endpoint and a packaged Claude skill (npm + skill package), semantic versioning, and developer docs contributed to the Stellar Developer Docs.
	•	Success criterion. A tagged release, published packages, and a docs PR opened to Stellar Developer Docs.
	•	Verification. Reviewer installs the published packages and views the docs PR.
	•	Stellar components. Stellar Developer Docs; the Soroban tooling above.
	•	Dependencies. D2.1, D2.2.
D3.4 — Mainnet demonstration · $5,500
	•	Description. Generate → simulate → install a policy on a mainnet Soroban smart account via the integrated wallet.
	•	Success criterion. A mainnet smart account with an installed generated policy; contract ID + transaction links shared.
	•	Verification. Reviewer views the mainnet smart account and installed policy on an explorer.
	•	Stellar components. Mainnet Soroban smart accounts; OZ accounts install flow; stellar-wallets-kit.
	•	Dependencies. D2.5, D3.2.
D3.5 — Test suite + audit readiness · $3,000
	•	Description. Comprehensive tests across synthesizer input shapes; a threat model and audit-prep for the synthesizer and generated templates, with the audit scope submitted to the Audit Bank (the audit itself is provided at T3 completion and is not budgeted).
	•	Success criterion. A coverage report; a threat-model document; audit scope submitted to the Audit Bank.
	•	Verification. Reviewer reads the coverage report and threat model; Audit Bank submission confirmed.
	•	Stellar components. Soroban Audit Bank; generated Policy contracts.
	•	Dependencies. D2.4, D3.1.

Testing & verification approach
	•	Unit tests cover the synthesizer (scope binding, gross-outflow caps, inflow-only minimal-permission case, argument constraints) and the simulation harness (every permit/deny/flag path).
	•	On-chain verification at each milestone uses real testnet (T1–T2) and mainnet (T3) deployments, evidenced by contract IDs and transaction links — not screenshots.
	•	Reproducibility. Generated Soroban policies build via stellar-cli in a digest-pinned container, so a reviewer can reproduce the Wasm.
	•	CI runs typecheck + tests + demo on every push from Tranche 1 onward.
Risk register
Risk
Likelihood
Mitigation
Wallet-integration coordination slips (external dependency)
Medium
Engage a C-Address cohort wallet early in T1; the install flow is the only externally-dependent deliverable, and the synthesizer/MCP/skill ship regardless
Generated-code correctness (authorization logic on funds)
Medium
Deny-case simulation; OZ review in T3; audit via Audit Bank before production; generated code labelled illustrative until audited
Soroban RPC retention limits live recording of older txs
Low
Simulated-tx path and an indexer adapter behind the same interface
OZ accounts package or stellar-cli version drift
Low
Target the latest stable releases; pin versions; track upstream
Solo-capacity vs. active Nectar award
Medium
Deliberately scoped, AI-augmented build; Nectar on track (T1 delivered/approved, T2 in progress); clear separation of work
Change management
Scope can flex, the destination cannot (a functional tool live on Stellar mainnet). Per SCF guidance, any tranche change is communicated to the SCF team before submitting a modified tranche, with updated success metrics or proof formats as needed.
Maintenance after the grant
MIT, public repo, semantic versioning, CI, and an open issue tracker; a versioned MCP endpoint and packaged skill; docs in the Stellar Developer Docs; coordination with OZ to upstream useful primitives; community updates via the Stellar Dev Discord and open channels (Mastodon / BlueSky). The core is stateless and local, so ongoing cost is low; XXIX Labs maintains Policywright as part of its developer-tooling portfolio.
