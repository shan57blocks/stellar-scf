# OZ Accounts Policy Builder by Gateway.fm

A developer tool for OpenZeppelin (OZ) smart accounts on Stellar. A smart account is a wallet run by a contract, so it can follow custom rules. A "policy" is a small contract that says what someone else, a person or an AI agent, may do with that wallet. Writing one by hand today is hard and risky. With this tool you point at a transaction you already made. It writes a policy that allows exactly that action and blocks everything else. The output is readable Rust code (Rust is the language of Stellar smart contracts), plus a test report. Nothing is deployed automatically. It is for wallet builders, developers, and AI-agent builders. It answers an SCF "RFP", a request for proposals: a tool the Stellar Community Fund asked teams to build. Gateway.fm is a Norwegian Web3 infrastructure company. It also runs a public Soroban RPC service, a server apps use to read from and send to Stellar.

## What SCF #44 pays them to build

Total award: $98,000.

- **Tranche 1 — MVP, about weeks 1–6 ($19,600, 20%).**
  - A "recording layer" that reads a real transaction.
  - A first "synthesizer" that turns the recording into a policy. It reuses OZ's ready-made `spending_limit` policy plus a small custom policy.
  - An MCP server, v0, with `record` and `synthesize` tools. MCP (Model Context Protocol) is a standard way for AI agents to call tools.
  - Proof: a recorded testnet transfer becomes Rust code that `stellar contract build` compiles.
- **Tranche 2 — Testnet, about weeks 7–14 ($29,400, 30%).**
  - A dry-run test harness. The original call must pass, and 5 changed versions must be blocked.
  - A Claude agent skill that asks for clarification when unsure and needs your confirmation before deploying.
  - Three worked examples on testnet: claiming yield on Blend (a lending app), a SEP-41 token subscription (SEP-41 is Stellar's standard token interface), and a limited Soroswap trade (Soroswap is a token exchange).
  - Wallet integration and a hosted testnet endpoint.
  - Proof: a public demo and video.
- **Tranche 3 — Mainnet, about weeks 15–20 ($39,200, 40%).**
  - A production endpoint, documentation, and a full test suite.
  - Fixes from a security audit, done through the SCF Soroban Security Audit Bank.
  - A signed review of the generated code by an OpenZeppelin reviewer.
  - One mainnet wallet integration.
  - Proof: a generated policy installed on mainnet, and a published audit report.

## How it works

The proposal describes seven parts:

1. **Recording layer.** It reads a transaction from Soroban RPC, the server interface to Soroban (Stellar's smart contract platform). It works with a real transaction (`getTransaction`) or a test run (`simulateTransaction`). It pulls out which contract and function were called, with which arguments, and which tokens moved.
2. **Synthesizer.** It turns that recording into the tightest rule it can: which contract, which function, how much, how often, and until when.
3. **Generated Rust policy.** A Soroban contract that implements OZ's `Policy` trait (a standard interface policies must follow). It reuses audited OZ policies first (`spending_limit`, `simple_threshold`, `weighted_threshold`). It writes new code only when it must.
4. **MCP server.** One Rust program that offers `record` / `synthesize` / `simulate` / `verify` tools. It is deterministic: the same input always gives the same output.
5. **Agent skill.** A Claude skill on top of the MCP tools. The AI only calls the tools. It never writes the policy bytes itself.
6. **Dry-run harness.** It replays the original call, which must pass. It then tries 5 changed versions, which must all fail: another contract, a bigger amount, outside the time window, another function, another recipient.
7. **Wallet integration.** Record → generate → simulate → sign → install, built on `smart-account-kit`, `passkey-kit`, and `launchtube`. It starts from `kalepail/pollywallet`, a demo wallet named in the RFP.

The repo's own architecture doc (v0.8) goes further. It turns the pipeline into separate Rust modules: recorder, PolicySpec (a written-down spec of the policy), synthesizer, and code generator. It adds an independent "reference evaluator" that checks a spec without sharing code with the generator. It also adds signed registries of known policies and account types. Tranche 1 is reported as done, with a guide for reviewers and a nightly live testnet check. `docs/SCOPE.md` lists what Tranche 1 leaves out on purpose.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recTFz7yIm9fWFHSx | submission.md | Only submission on the project page, so no earlier rounds |
| Technical architecture (gist) | architecture | https://gist.github.com/revitteth/191331235c2f68ba307941cbf8060867 | architecture.md | The doc linked in the submission |
| Technical Architecture & Delivery Plan v0.8 (repo) | architecture / spec | https://github.com/gateway-fm/oz-policy-builder/blob/main/docs/architecture.md | repo-architecture-v0.8.md | Much longer and more detailed than the gist |
| Repo README | docs | https://github.com/gateway-fm/oz-policy-builder | repo-readme.md | Pipeline, design rules, code layout |
| Scope: what this milestone leaves out | spec | https://github.com/gateway-fm/oz-policy-builder/blob/main/docs/SCOPE.md | scope.md | Items planned for later, with reasons |
| Tranche 1 verification guide | evidence | https://github.com/gateway-fm/oz-policy-builder/blob/main/docs/TRANCHE-1-VERIFICATION.md | tranche-1-verification.md | Step-by-step checks for reviewers |
| Testnet evidence | evidence | https://github.com/gateway-fm/oz-policy-builder/blob/main/docs/TESTNET-EVIDENCE.md | testnet-evidence.md | Live testnet runs |
| Tranche 1 milestone issue #1 | evidence | https://github.com/gateway-fm/oz-policy-builder/issues/1 | — | What was contracted, and the status of each item |
| MCP walkthrough | docs | https://github.com/gateway-fm/oz-policy-builder/blob/main/docs/MCP-WALKTHROUGH.md | — | How to use the MCP tools |
| Developer guide | docs | https://github.com/gateway-fm/oz-policy-builder/blob/main/docs/DEVELOPERS.md | — | For people working on the code |
| Ecosystem conformance | spec | https://github.com/gateway-fm/oz-policy-builder/blob/main/docs/ECOSYSTEM-CONFORMANCE.md | — | 87 KB; checks against OZ and Stellar behaviour |
| Canonical hashing | spec | https://github.com/gateway-fm/oz-policy-builder/blob/main/docs/CANONICAL-HASHING.md | — | How artifact hashes are computed |
| kalepail/pollywallet | prior work | https://github.com/kalepail/pollywallet | — | Starting-point wallet named in the RFP |
| SCF RFP track page | RFP | https://stellar.gitbook.io/scf-handbook/scf-awards/build-award/rfp-track | — | Current page no longer lists this RFP |
| Gateway.fm website | website | https://gateway.fm | — | Company site |

## Gaps

- The original SCF RFP text for the "OZ Accounts Policy Builder" was not found. The live RFP track page now shows other RFPs.
- No pitch deck, demo video, or audit yet. The demo video is due in Tranche 2, the audit in Tranche 3.
- The submission says Gateway's MCP server experience comes from an internal, non-public tool, so it cannot be checked.
- Tranche 2 parts (harness, agent skill, wallet) are only planned in the docs. Tranche 1 is reported complete. SCF acceptance is still pending.
