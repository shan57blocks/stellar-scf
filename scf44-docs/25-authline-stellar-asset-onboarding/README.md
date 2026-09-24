# Authline (Trustline Onboarder & SDK)

On Stellar, you cannot receive a "classic" token (an asset issued by an account, like USDC or EURCV) until you open a **trustline** (a small record on your account that says "I accept this asset"). A trustline costs a 0.5 XLM deposit. Some regulated tokens also need the issuer to **authorize** each trustline. Today, when an exchange pays out a token to a user without a trustline, the transfer fails. Authline lets the third party (an exchange, broker or wallet) do almost all of this work for the user. The user signs at most once, and sometimes never. It is built by The Aha Company, the team behind the Stellar CLI and Scaffold Stellar. It generalizes their live mainnet onboarding contract for Société Générale-Forge's EURCV euro token. It is for exchanges, wallets, brokers and asset issuers.

## What SCF #44 pays them to build

Total award: $75.0K (Developer Tooling).

- **Tranche 1 – MVP ($17,500)**
  - D1.1 ($11,000, due Jul 17 2026): freeze the draft standard (a SEP, a Stellar Ecosystem Proposal) and deploy the onboard router contract on testnet. Tested for success, rejection with rollback, failed check, and assets with no authorizer.
  - D1.2 ($6,500, due Jul 31 2026): TypeScript SDK `@theaha/authline` (pre-1.0 on npm) and the Authline web app on testnet, for both open and regulated assets.
- **Tranche 2 – Testnet ($27,000)**
  - D2.1 ($9,000, due Aug 21 2026): the three onboarding cases (A, B, C), claimable-balance delivery, wallet handoffs (SEP-7 link, deep link, redirect), and an exchange-withdrawal demo.
  - D2.2 ($10,500, due Sep 4 2026): a Trustline Authorizer contract for any asset (denylist or allowlist, ban, freeze, pause, upgrade) plus an issuer admin CLI and runbook.
  - D2.3 ($7,500, due Sep 18 2026): unit and end-to-end test suites, a hosted relayer (2 HTTP endpoints, also a Docker image), a MiCA design note (MiCA = EU crypto-asset rules), and an updated SEP.
- **Tranche 3 – Mainnet ($30,500)**
  - D3.1 ($12,000, due Oct 2 2026): router and Authorizer on mainnet (target: EURCV, else their own regulated asset), plus audit fixes.
  - D3.2 ($9,500, due Oct 23 2026): adoption by one wallet and one exchange/broker (target: Bitpanda), with real mainnet onboardings.
  - D3.3 ($9,000, due Oct 30 2026): final SEP pull request to stellar-protocol, integration guide and docs site, SDK 1.0, SCF user testing, 14 days stable on mainnet.

## How it works

- **Onboard router** (a Soroban smart contract; Soroban is Stellar's smart-contract platform). One call, `onboard(sac, holder)`, with one user signature. It creates the trustline (using CAP-73, a protocol change that lets contracts create trustlines). It looks up the asset's admin on-chain (CAP-68). If the admin is an authorizer contract, it authorizes in the same transaction, then re-checks the ledger. It returns `Authorized` or `TrustlineOnly`. If the authorizer refuses, everything rolls back. The router has no state and no admin.
- **Trustline Authorizer** (Soroban contract). The issuer installs it as the admin of its token contract (the SAC, Stellar Asset Contract). Anyone can ask it to authorize an account; a policy (denylist or allowlist) decides. It supports ban, freeze, pause and upgrade, and logs every decision as an on-chain event.
- **Integrator SDK** (`@theaha/authline`, TypeScript). Builds the three transaction shapes:
  - Case A: trustline exists but is not authorized. Anyone authorizes it. Zero user signatures.
  - Case B: user has nothing. The platform pays the deposit and creates the account (CAP-33 "sponsorship"). The user signs once.
  - Case C: the router path above.
  It also checks readiness, does wallet handoffs (SEP-7 signing links), pins known asset and router addresses so a fake authorizer cannot be swapped in, and includes a small React layer and the admin CLI.
- **Relayer** (optional, self-hostable Docker service). `GET /status` and `POST /authorize`. Lets an exchange integrate in about 20 lines of any language.
- **Claimable-balance delivery**. If the user is not ready, the exchange sends a claimable balance (funds the user can pick up later) instead of a payment. The user's single claim also opens the trustline.
- **Discovery**. The issuer adds a `[TRUSTLINE_ONBOARDER]` block to its `stellar.toml` (the standard public config file of a Stellar issuer).
- **Authline web app** (Vite + React, no backend) at testnet.authline.io. Users connect a wallet through Stellar Wallets Kit and activate an asset.
- **Integration ladder** for platforms: Tier 0 link to the activation page; Tier 1 relayer; Tier 2 claimable balances; Tier 3 full SDK.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recVyBmi9h4ycsMy6 | submission.md | Tranches, budget, completion criteria |
| SCF project page | earlier SCF submission | https://communityfund.stellar.org/project/recGbY82R8bt5NyH4 | — | Lists only SCF #44; no earlier rounds for this project |
| Technical architecture (Google Doc, .docx) | architecture | https://docs.google.com/document/d/1qlIGWzJgLmgjEbMUbREtkkznDJ63WtN5/edit | architecture.txt, architecture.docx | Full engineering companion: router, Authorizer, SDK, relayer, security, risk register, MiCA mapping |
| SEP draft: Trustline Onboarder | spec | https://github.com/theahaco/authline/blob/main/sep/sep_trustlineonboarder.md | sep-trustline-onboarder.md | The proposed standard |
| SEP public discussion | spec | https://github.com/orgs/stellar/discussions/2008 | — | "Trustline Onboarder - third-party trustline onboarding for classic assets" |
| Integrator SDK guide | docs | https://github.com/theahaco/authline/blob/main/docs/authline-sdk.md | sdk-guide.md | |
| STRIDE threat model | spec | https://github.com/theahaco/authline/blob/main/docs/threat-model.md | threat-model.md | Security analysis (a PDF version is also in the repo) |
| MiCA design note | spec | https://github.com/theahaco/authline/blob/main/docs/mica-authorization-model.md | mica-authorization-model.md | What goes on-chain, why no personal data (D2.3) |
| Relayer runbook | docs | https://github.com/theahaco/authline/blob/main/docs/relayer-runbook.md | — | HTTP API, Docker, operations |
| Authorizer admin CLI runbook | docs | https://github.com/theahaco/authline/blob/main/docs/authorizer-runbook.md | — | |
| SEP-7 handoff | docs | https://github.com/theahaco/authline/blob/main/docs/sep7-handoff.md | — | How a wallet signs an onboarding request |
| Testnet evidence (Authorizer, relayer) | demo | https://github.com/theahaco/authline/tree/main/docs | — | `authorizer-testnet-evidence.md`, `relayer-evidence.md` |
| Design specs and plans | spec | https://github.com/theahaco/authline/tree/main/docs/superpowers | — | Dated design notes, e.g. router runtime discovery (Jun 2026) |
| SDK on npm | docs | https://registry.npmjs.org/@theahaco/authline | — | `@theahaco/authline`, latest 0.8.1 (npmjs.com web page blocks scripts; registry link used) |
| Code repo | docs | https://github.com/theahaco/authline | — | Apache-2.0; tags up to v0.8.1. Root README is mostly Scaffold Stellar boilerplate plus a doc index |
| Authline testnet app | demo | https://testnet.authline.io | — | Also served at https://theahaco.github.io/authline |
| EURCV activation page (precursor) | demo | https://eurcv.theaha.co | — | Live mainnet predecessor |
| EURCV authorizer contract on mainnet | demo | https://stellar.expert/explorer/public/contract/CB2DHZMQHQE3TGUMD6BRM7UCJZNIPKDRVEQOWBIRRS3G2FZOGDTRKSB3 | — | Traction evidence |

## Gaps

- The submission names the SDK `@theaha/authline`, which does not exist on npm. It is published as `@theahaco/authline` (latest 0.8.1). SDK 1.0 is a Tranche 3 deliverable.
- No separate docs website or integration guide yet (due in D3.3).
- No pitch deck, audit report or demo video linked.
- The architecture doc diagrams are images inside the .docx; only the text is in architecture.txt.
