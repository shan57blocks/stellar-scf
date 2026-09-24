# CredioLabs.AI: OZ Accounts Policy Builder

Credio Labs (a team incubated by Untangled Finance, UK) builds the OZ Policy Builder. It is a tool for developers who want to give an AI agent limited permission on a Stellar "smart account" (an account run by a Soroban smart contract, here the OpenZeppelin (OZ) Stellar Accounts contracts). Today a developer must write those permission rules by hand. The tool records a real transaction (for example "claim my Blend yield"), writes the smallest rule that allows exactly that action, tests that the rule blocks similar but wrong actions, and hands the user an unsigned transaction to install the rule from their wallet (Freighter). It is offered as an MCP server (Model Context Protocol, a standard way for AI assistants to call tools), a Claude skill, a CLI, and a hosted service at agents.crediolabs.ai.

## What SCF #44 pays them to build

Total award: $125,000. The architecture doc pays it as 10% at kickoff ($12,500), then 20% / 30% / 40% on the three tranches.

- **Tranche 1 — Foundation, weeks 1–2 ($25,000):** transaction recorder (by hash or raw XDR, Stellar's binary transaction format) ($10,000); synthesizer v1 that picks OZ built-in policies (`simple_threshold`, `weighted_threshold`, `spending_limit`) ("Path A") or flags that new policy code is needed ("Path B") ($10,000); MCP server skeleton with `record_transaction` and `synthesize_policy` ($5,000); send design materials to the OZ Stellar team for review. Done when: both tools work on 3 sample transactions (Blend `claimRewards`, SEP-41 token `transfer`, SoroSwap swap) and the builds pass.
- **Tranche 2 — Synthesis hardening, weeks 3–4 ($37,500):** Path B Rust policy code generation with a mandatory `cargo check` gate ($7,000); a "deny-case harness" that tests 7 kinds of wrong transactions (amount, asset, contract, function, timing, time window, capacity) ($16,000); Claude skill with a 4-step chat flow ($5,500); "minimality checker" that removes each constraint and keeps only the ones that matter ($9,000).
- **Tranche 3 — Integration, walkthroughs and 1.0 release, weeks 5–10 ($50,000):** Freighter install flow ($9,500); check wallet compatibility with the C-Address Tooling cohort ($500); `verify_policy` and `install_policy` tools ($3,000); three walkthroughs with live testnet receipts and videos (Blend yield claim, SEP-41 subscription, bounded SoroSwap trade) ($6,000); developer docs at agents.crediolabs.ai/docs ($1,500); test suite on ≥10 transaction shapes with green CI ($13,500); independent audit ($3,000) and fixing its findings; a `frequency_limit` pull request to OZ ($1,500); monthly devlogs ($500); v1.0.0 release on npm and the Anthropic skill marketplace ($2,500); post-1.0 maintenance ($8,500).

## How it works

**As proposed (Notion architecture doc):** a Rust "synthesis engine" does the work, and a thin TypeScript MCP server exposes it as 5 tools in a pipeline: `record_transaction` → `synthesize_policy` → `simulate_policy` (deny-case harness) → `verify_policy` (minimality check) → `install_policy`. The last step returns an unsigned transaction. The user reviews it and signs it in Freighter, which calls `add_context_rule` on their OZ smart account. A "context rule" is the smart account's rule saying which signers and policies apply to which calls. The design reuses the team's earlier Stellar work: OctoPos (XDR parsing, transaction building), the OctoGear MCP server, and two audited Soroban contracts (OctoLend, Untangled Vault).

**As built (public repo, September 2026):** the design changed. Instead of generating new Rust policy code per rule, there is one on-chain Soroban contract, the `policy-interpreter`, deployed on mainnet and testnet. Each policy is just data: a "predicate" (a true/false rule tree, such as "method is `transfer` and amount ≤ 50") that the interpreter checks on every call and rejects if it fails. Off chain, the TypeScript package `@crediolabs/policy-synth` records a transaction, turns it into a predicate, shows a review card, and returns an unsigned install transaction. A CLI and an MCP server (`@crediolabs/policy-builder-mcp`, v1.3.0 on npm) wrap the same core. The repo says the off-chain side only compiles rules; only the on-chain interpreter enforces them. The repo also says the interpreter has not been externally audited yet; an external review is in progress.

Stellar features used: Soroban smart contracts, OpenZeppelin Stellar smart accounts (context rules and policies), SEP-41 tokens, Freighter wallet, Soroban RPC.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recUpUVSNstKqoxlm | submission.md | Tranches, budget, completion criteria |
| OZ Accounts Policy Builder — Technical Architecture (Notion) | architecture | https://app.notion.com/p/untangledfi/OZ-Accounts-Policy-Builder-by-Crediolabs-ai-37f08dd274ad80c595a7db54f2b62cdf | architecture.md | Public page, text pulled through Notion's page API. Includes gap analysis, risk register, and budget per sub-deliverable. Diagrams kept as text. |
| Repo README | docs | https://github.com/untangledfinance/oz-policy-builder | repo-readme.md | Deployed contract addresses, audit status |
| Repo architecture | architecture | https://github.com/untangledfinance/oz-policy-builder/blob/main/docs/architecture.md | repo-architecture.md | The design as built (interpreter + predicate grammar) |
| STRIDE threat model | spec | https://github.com/untangledfinance/oz-policy-builder/blob/main/docs/stride-threat-model.md | repo-threat-model.md | Security threat model, dated 2026-08-23 |
| Audit evidence | audit | https://github.com/untangledfinance/oz-policy-builder/blob/main/docs/audit/README.md | repo-audit-status.md | Internal tool runs (cargo audit, scout, tests); not an external audit report |
| Devlog, August 2026 | blog | https://github.com/untangledfinance/oz-policy-builder/blob/main/docs/devlog/2026-08.md | repo-devlog-2026-08.md | Monthly progress update |
| Audit handover, changelog, release notes | docs | https://github.com/untangledfinance/oz-policy-builder/tree/main/docs | — | Not copied; in repo |
| npm package `@crediolabs/policy-builder-mcp` | docs | https://registry.npmjs.org/@crediolabs%2fpolicy-builder-mcp | — | Latest 1.3.0. Name differs from the proposal's `@credio/policy-builder-mcp`, which does not exist on npm. |
| kalepail/pollywallet | earlier work (other team) | https://github.com/kalepail/pollywallet | — | Prototype this project builds on, per the submission |
| Credio Labs website | docs site | https://crediolabs.ai/ | — | Almost no text content |
| Untangled SCF project page | earlier SCF submission | https://communityfund.stellar.org/project/untangled-qcs | — | Same founders. SCF #30 (prescreen failed), SCF #31 (awarded $150K), Liquidity Award '25 Q3 ($50K) |
| Untangled SCF #31 submission | earlier SCF submission | https://communityfund.stellar.org/submissions/rec5hF8af5tfhSlT2 | earlier-scf31-untangled-vault.md | Untangled Vault + Credio oracle on Soroban |
| Untangled SCF #30 submission | earlier SCF submission | https://communityfund.stellar.org/submissions/rec5iNa5hfaUZs1SI | — | Prescreen failed; not copied |
| Untangled Vault contract | earlier work | https://github.com/untangledfinance/soroban-vault-contract | — | From the SCF #31 work |
| OctoPos agent repo | earlier work | https://github.com/untangledfinance/credio-agents | — | Named "Repository" in the Notion doc. Holds an OctoPos risk agent, not this tool. |
| SCF #31 architecture doc (Google Doc) | architecture | https://docs.google.com/document/d/1p9XyJo8uuvMboWFaF-QS4KgWhx1ZSjqsytIxDyDDPKo/edit | — | Private, could not open (sign-in required) |
| Untangled pitch deck (Google Slides) | pitch | https://docs.google.com/presentation/d/1loxPEFtkhAdb6VXvdPwQ2qkVxQnuS617uy4rAiMCuxo/edit | — | Private, could not open (sign-in required) |

## Gaps

- The hosted service at https://agents.crediolabs.ai and the promised docs at https://agents.crediolabs.ai/docs sit behind a Cloudflare Access login, so they could not be read.
- There is no external audit report yet. The repo says one is in progress.
- The SCF #31 Google Doc and the Untangled pitch deck are private.
- Links from the SCF #31 submission are broken: the Verilog audit PDF (404) and the Credio whitepaper at credio.network/docs/whitepaper (404).
- The submission names OctoPos (SCF #41) as the team's earlier Stellar work. Its SCF project page could not be found, so its submission was not collected.
- The walkthrough videos and testnet receipts promised for Tranche 3 were not found in public sources.
