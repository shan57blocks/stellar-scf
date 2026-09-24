Source: https://policywright.lemmalabs.space/roadmap/


Roadmap
Status: Shipped — site-delta map
Built in response to the SCF ‘OZ accounts policy builder’ RFP (Q2 2026), funded
in round SCF #44 as the awarded submission ‘Record-to-Policy MCP + Agent
skill.’ All dates below are targets. Shipped claims link proof in-repo or
on-chain; human session videos are [BLOCKER] until Phase 5 evidence.
The three tranches
Section titled “The three tranches”
 | 
 | Tranche
 | Target
 | Focus
 | Status
 | T1 — MVP (testnet)
 | 31 Aug 2026 (delivered early)
 | Working engine
 | Delivered + approved — see below
 | T2 — Testnet expansion
 | 15 Oct 2026 (target)
 | Agent-usable tooling
 | In progress / largely shipped — per-row proof
 | T3 — Mainnet launch
 | 30 Nov 2026 (target)
 | Production
 | Planned only — Audit Bank audit listed; no T3 work claimed
T1 — delivered early + approved
Section titled “T1 — delivered early + approved”
 | 
 | Deliverable
 | Proof
 | Recording (fixture + live RPC + simulation)
 | src/sources/, examples/live/
 | Least-privilege synthesizer + installable OZ context rules
 | src/synthesizer.ts, docs/context-rule-schema.md
 | Generated policy compile + hash-verified testnet deploy
 | CDSVPSTS…, EVIDENCE.md
 | Open-source CLI + CI
 | src/cli.ts, CI workflow
T2 — per deliverable
Section titled “T2 — per deliverable”
 | 
 | Deliverable
 | Status
 | Proof
 | MCP server (record / synthesize / simulate / verify)
 | Shipped (local stdio)
 | docs/mcp-server.md, test/mcp-stdio.test.ts. [BLOCKER] human reference session recording
 | Claude skill
 | Shipped (packaged)
 | skills/policywright/SKILL.md, docs/skill-demo-script.md. [BLOCKER] human demo recording
 | Dry-run harness + argument-level scope
 | Shipped
 | docs/argument-scope.md, simulation-report-args-off.md / args-on.md
 | Compose + net-new codegen (storage segregation)
 | Shipped
 | docs/compose-vs-generate.md, simulation-report-compose-and-generate.md
 | Wallet / smart-account install (testnet)
 | Shipped (testnet)
 | docs/smart-account-install.md, SA CAXBVHXP…, EVIDENCE D2.5
T3 — Planned (target 30 Nov 2026)
Section titled “T3 — Planned (target 30 Nov 2026)”
Listed only — not started in this repository:
Three documented end-to-end walkthroughs (production packaging)
OpenZeppelin validation of generated code
Versioned MCP + packaged skill + docs release
Mainnet demonstration
Test suite + audit scope submitted to the SCF Audit Bank
Until the Audit Bank audit, generated contracts remain illustrative /
unaudited and testnet-only.
Proof policy
Section titled “Proof policy”
Shipped claims on this site link to the repository, CI, committed artefacts, or
explorer contract/tx IDs. Missing human session recordings are marked
[BLOCKER], not invented.
Previous
Security
Next
CLI
