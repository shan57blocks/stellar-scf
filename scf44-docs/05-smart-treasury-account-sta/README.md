# Smart Treasury Account (STA)

Smart Treasury Account is a shared company wallet on Stellar that follows rules written into code. A team sets who may approve payments, which assets and recipients are allowed, and how much can be sent. The wallet then refuses anything outside those rules. It also supports scheduled payments, payroll, revenue splits, and emergency freeze and recovery. It is for companies, payment apps, marketplaces, DAOs (online groups that manage shared funds) and token issuers that pay people in stablecoins (coins pegged to a currency like the US dollar). Team lead: Reto Grau. SCF #44 award: $127.5K.

## What SCF #44 pays them to build

- **Tranche 1 — MVP ($31,000, 1.5 months)**
  - Smart contracts ($21,000): signers with weights, approval thresholds, replay protection (the same payment cannot be sent twice), pause/freeze, allowed assets and recipients, amount checks, payments. Uses OpenZeppelin's audited Stellar libraries for admin and pause. The new part is the "policy engine", whose rules carry a version number so later rule changes cannot alter payments already approved.
  - Tests and documentation ($10,000), plus testnet deploy scripts.
- **Tranche 2 — Testnet ($41,500, 1.5 months)**
  - Deploy contracts on testnet (Stellar's test network) ($7,000).
  - Web app with wallet connection (Freighter, xBull), dashboard, signer and policy screens, payment simulate/approve/submit ($21,000).
  - Relayer ($13,500): a background service that sends scheduled payments. It cannot hold funds or skip the rules, and each scheduled run can execute only once.
- **Tranche 3 — Mainnet ($55,000, 1.5 months)**
  - Mainnet (live network) contracts and a TypeScript SDK (a code library for developers) ($17,000).
  - Production web app and relayer monitoring and alerts ($25,400).
  - Operator guide, SDK guide, deployment notes, testing guide, release checklist ($12,600).

## How it works

- Each treasury is a **SmartAccount** contract. A contract is a program running on Stellar's Soroban smart-contract platform. The account holds the money. It checks every approval itself through Soroban's custom-account hook (`__check_auth`).
- Signers are normal wallet keys or passkeys (phone or laptop fingerprint/face logins). Signer lists and threshold math come from OpenZeppelin's Stellar contracts, not custom code.
- **PolicyEngine** checks allowed assets, recipients, operations, amount caps and the policy version.
- **IntentRegistry** stores scheduled payments and makes sure each scheduled run executes once, within its time window.
- **RecoveryManager** handles guardians (backup approvers), freeze, and time-delayed recovery.
- **TransferAdapter** and **SplitAdapter** do the actual token moves (one recipient, or split across many). They move funds only for the exact action the account approved.
- **WebAuthn verifier** checks passkey signatures.
- An **account factory** contract creates new treasuries.
- Assets are Stellar Asset Contracts only (SAC: the contract form of normal Stellar tokens such as XLM and USDC).
- Off-chain parts: a web app (Stellar Wallets Kit for Freighter/xBull, Stellar RPC to simulate transactions before signing), a relayer that submits scheduled payments, and the `sta-sdk` npm package.
- Status per the team (2026-09-10/11): seven contracts on testnet, six on mainnet, 120 tests passing, and all three tranches reported as done in their own verification reports.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recmj5cqlrqKyd1Bc | submission.md | Tranches, budgets, completion criteria |
| Technical Architecture | architecture | https://github.com/Smart-Treasury-Account-STA/smart-contracts/blob/main/docs/TECHNICAL_ARCHITECTURE.md | architecture.md | Full target design, diagrams, workflows, security |
| V1 Scope | spec | https://github.com/Smart-Treasury-Account-STA/smart-contracts/blob/main/docs/V1_SCOPE.md | v1-scope.md | What was built, what came from OpenZeppelin, what is deferred, review fixes |
| Smart Contract Specification | spec | https://github.com/Smart-Treasury-Account-STA/smart-contracts/blob/main/docs/SMART_CONTRACT_SPECIFICATION.md | contract-spec.md | Module-by-module design |
| dApp Integration Spec | spec | https://github.com/Smart-Treasury-Account-STA/smart-contracts/blob/main/docs/DAPP_INTEGRATION_SPEC.md | dapp-integration-spec.md | How the app and relayer talk to the contracts |
| Smart Contract Audit Report | audit | https://github.com/Smart-Treasury-Account-STA/smart-contracts/blob/main/docs/SMART_CONTRACT_AUDIT_REPORT.md | audit-report.md | Review of the contracts; author not named. Two medium findings |
| Strict security review | audit | https://github.com/Smart-Treasury-Account-STA/smart-contracts/blob/main/docs/SECURITY_REVIEW_STRICT.md | — | Long internal review (80 KB) |
| Testnet deployment record | spec | https://github.com/Smart-Treasury-Account-STA/smart-contracts/blob/main/docs/TESTNET_DEPLOYMENT.md | testnet-deployment.md | Linked in submission. Contract IDs and transactions |
| Mainnet deployment record | spec | https://github.com/Smart-Treasury-Account-STA/smart-contracts/blob/main/docs/MAINNET_DEPLOYMENT.md | — | Mainnet IDs, WASM hashes |
| Governance multisig design | architecture | https://github.com/Smart-Treasury-Account-STA/smart-contracts/blob/main/docs/GOVERNANCE_MULTISIG_DESIGN.md | — | Design notes only, not built |
| Docs folder index | docs site | https://github.com/Smart-Treasury-Account-STA/smart-contracts/blob/main/docs/README.md | — | Reading order for all repo docs |
| Contracts repo README | docs site | https://github.com/Smart-Treasury-Account-STA/smart-contracts | repo-readme.md | Linked in submission |
| Tranche 2 verification report | spec | https://github.com/Smart-Treasury-Account-STA/dApp/blob/main/docs/TRANCHE_2_VERIFICATION.md | tranche-2-verification.md | Team's evidence for Tranche 2 |
| Tranche 3 verification report | spec | https://github.com/Smart-Treasury-Account-STA/dApp/blob/main/docs/TRANCHE_3_VERIFICATION.md | tranche-3-verification.md | Team's evidence for Tranche 3 (dated 2026-09-11) |
| Implementation status | docs site | https://smarttreasury.io/docs/status | implementation-status.md | Copied from the docs repo `status.md` |
| Docs website | docs site | https://smarttreasury.io/docs | — | Operator, SDK, security, testing pages |
| Website | pitch | https://smarttreasury.io | — | Product landing page |
| Production app | demo | https://smarttreasury.io/app | — | Mainnet dApp |
| SDK repo | docs site | https://github.com/Smart-Treasury-Account-STA/sdk | — | npm package `sta-sdk` (latest 0.2.1) |
| dApp repo | docs site | https://github.com/Smart-Treasury-Account-STA/dApp | — | App and relayer code, developer guide |
| SCF project page | earlier SCF submission | https://communityfund.stellar.org/project/recSqbVkx1zZdY9Ly | — | Only one submission (SCF #44). No earlier rounds |

## Gaps

- The PoC Review Guide linked in the submission (`docs/POC_REVIEW_GUIDE.md`) now returns 404. The repo rewrote it as `docs/V1_REVIEW_GUIDE.md`. The old text exists only in git history.
- The npm page (npmjs.com/package/sta-sdk) blocks automated requests (HTTP 403). The package is confirmed through the npm registry API.
- No pitch deck, whitepaper, demo video or blog post is linked anywhere.
- The audit report does not name who wrote it. There is no named third-party audit firm.
- No earlier SCF submissions exist. The SCF project page lists `reto-grau-consulting.ch` as the website, not smarttreasury.io.
