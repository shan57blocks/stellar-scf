# Policywright

Policywright is an open-source developer tool (MIT license) from XXIX Labs. It looks at a Stellar transaction you already made (or simulated) and writes the tightest permission rule that allows exactly that action again, and nothing else. The rule is for an OpenZeppelin smart account on Soroban (a "smart account" is a wallet that is itself a contract, with a `C…` address, and can give limited powers to others). It is for developers and for people who want to let an AI agent act for them without handing over their full keys. It answers the SCF "OZ accounts policy builder" RFP (a request for proposals written by SCF) and builds on Tyler's (kalepail) `pollywallet` prototype. It was awarded $55K in SCF #44 (Developer Tooling).

## What SCF #44 pays them to build

Total $55,000, paid 10% on approval then 20/30/40%. Target dates are from the milestone plan (assuming a mid-July kickoff).

- **Tranche 1: MVP on testnet — $16,500 (10% + 20%), target 15 Aug 2026.**
  - Recording layer: read a real transaction by hash or a simulated one — $5,000
  - Least-privilege synthesizer: scope rule plus a stock OZ `spending_limit` — $6,000
  - Generated policy contract compiles and deploys to testnet — $3,500
  - Open-source CLI, repo and CI — $2,000
- **Tranche 2: testnet expansion — $16,500, target 30 Sep 2026.**
  - MCP server (MCP = Model Context Protocol, a standard way for AI agents to call tools) with `record`, `synthesize`, `simulate`, `verify` — $4,500
  - Agent skill (a chat front-end for the MCP) — $3,000
  - Dry-run harness with deny cases and argument limits — $3,500
  - New stateful policy code generation, storage kept separate per account and rule — $2,500
  - Wallet integration: install a generated policy on a testnet smart account — $3,000
- **Tranche 3: mainnet — $22,000, target 15 Nov 2026.**
  - Three end-to-end walkthroughs (Blend yield claim, SEP-41 subscription, bounded Soroswap delegation) — $5,000
  - OpenZeppelin review of generated code — $3,000
  - Production release: versioned MCP server, packaged skill, Stellar docs PR — $5,500
  - Mainnet demo: policy installed on a mainnet smart account — $5,500
  - Test suite, threat model, audit scope sent to the SCF Audit Bank (the audit itself is not in the budget) — $3,000

## How it works

It is a five-step pipeline. It runs on the user's own machine. There is no server that holds funds or keys.

1. **Record.** Read a transaction from Soroban RPC (`getTransaction`) or a `simulateTransaction` result. Decode it into which contracts and functions were called, with what arguments, and which tokens moved (from SEP-41 / Stellar Asset Contract `transfer` events).
2. **Synthesize.** Build a "context rule" (OZ's term for "this signer may call these functions") limited to exactly the observed contract-and-function pairs. Add limits: spending caps based on how much of each token left the account (gross outflow, not net), how long the rule lasts, how often it can run, and optionally which arguments are allowed (for example the swap path).
3. **Emit.** Use stock OZ policies first (`spending_limit`, `simple_threshold`, `weighted_threshold`). Only when they cannot express a limit, generate a new Soroban `Policy` contract in Rust. Output a JSON spec, `context-rule.json`, a plain summary, and the Rust code.
4. **Dry-run.** Check the rule allows the original flow and blocks nearby ones: another token, a bigger amount, outside the time window, too many repeats. Flag cases that pass but look too broad.
5. **Install.** The user reviews and signs client-side, then installs the policy on their smart account through a wallet (stellar-wallets-kit). Never automatic.

The MCP server and the agent skill sit on top so an AI agent can drive the steps. Built with TypeScript/Node, Rust with `soroban-sdk`, and `stellar-cli`.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recdYirBtCwecAipc | submission.md | Tranches, budgets, completion criteria |
| SCF project page | earlier SCF submission | https://communityfund.stellar.org/project/recVBIZIpny5d5j8x | — | Lists only this SCF #44 submission. The team's other SCF award (Nectar Network, SCF #42) is a separate project |
| Architecture folder (Google Drive) | architecture | https://drive.google.com/drive/folders/1DWKsFcLIS5HSGjCBP55rZBi-w0dut6kJ | — | Public folder with the three .docx files below |
| Technical Architecture | architecture | https://drive.google.com/file/d/1Hh5XZdz4pRGjRz_nyITmZWFB9qIwl7dD/view | architecture.md | Pipeline diagram is an image; described in "How it works" |
| Milestone & Deliverable Plan | requirements | https://drive.google.com/file/d/1keCLSG-megDf7iLDd73fGwUuDGpwLXYq/view | milestone-plan.md | Per-deliverable success criteria, budget, target dates |
| Ecosystem Coordination Plan | spec | https://drive.google.com/file/d/1KlH8w1jX95htOmpuDVU1Ba1_QIu_924w/view | coordination-plan.md | Roles of OpenZeppelin, the C-Address wallet cohort, kalepail, SDF |
| GitHub repo | spec | https://github.com/kunaldrall29/policywright | repo-readme.md | README: how it works, quickstart |
| Repo architecture doc | architecture | https://github.com/kunaldrall29/policywright/blob/main/docs/architecture.md | repo-architecture.md | Code-level stages and design choices |
| Repo docs folder | spec | https://github.com/kunaldrall29/policywright/tree/main/docs | — | MCP server, context-rule schema, argument scope, smart-account install, tranche notes |
| Evidence log | demo | https://github.com/kunaldrall29/policywright/blob/main/evidence/EVIDENCE.md | — | Testnet contract IDs and transaction proof per deliverable |
| Docs website | docs site | https://policywright.lemmalabs.space | — | Getting started, concepts, CLI/MCP/skill reference, use cases, security |
| Docs site roadmap | docs site | https://policywright.lemmalabs.space/roadmap/ | roadmap.md | Says T1 delivered and approved; T2 mostly shipped; T3 not started |
| kalepail/pollywallet | spec | https://github.com/kalepail/pollywallet | — | Prior-art MVP this project extends |
| Nectar Network (team's SCF #42 project) | blog | https://nectarnetwork.fun | — | Traction evidence, not this project |
| PowerGrid Network (team's Web3 Foundation grant) | blog | https://github.com/kunal-drall/powergrid_network | — | Traction evidence, not this project |

## Gaps

- The diagrams in the Drive .docx files (pipeline, coordination map, timeline) are images; the local copies have only their captions.
- The docs site roadmap marks the human-recorded demo sessions for the MCP server and the skill as "[BLOCKER]" (not yet recorded).
- Tranche 3 items (mainnet demo, OZ review, audit scope) are not public yet.
- No earlier SCF submissions exist for this project.
