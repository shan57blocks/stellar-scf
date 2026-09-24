# Account Demolisher

Account Demolisher is a web tool that fully closes a Stellar account and gives back the XLM it had locked up. Every Stellar account must keep 1 XLM, plus 0.5 XLM for each extra item it holds (a "subentry": a trustline, an open offer, an extra signer, a data entry). To get that money back you must remove every item and then run `ACCOUNT_MERGE` (the operation that deletes an account and sends its XLM to another account). The tool does all of this in one guided session. It also exits positions in Soroban DeFi apps (Soroban is Stellar's smart-contract platform) such as Blend, Aquarius, Soroswap and FxDAO. The older tool it replaces, stellar.expert/demolisher, cannot do that. It is for anyone with old, unused, or "zombie" accounts, and for wallets and exchanges that want to reuse the logic. The team is one person, Salih Toruner. SCF #44 award: $120.0K.

## What SCF #44 pays them to build

- **Tranche 1: MVP (about week 6), $24,000.** Classic account closure on testnet.
  - Account scan and feasibility check. It refuses accounts that can never close. $8,000.
  - Classic teardown engine, plus a "mediator" merge for sending to exchanges. $10,000.
  - Wallet connection (Freighter, Ledger, xBull, LOBSTR and others) and a 5-step closure screen. $6,000.
- **Tranche 2: Testnet (about week 12), $36,000.** The Soroban part.
  - Find and empty Soroban token balances, and list and cancel token "allowances" (permissions to spend your tokens). Includes a standalone allowance viewer. $8,000.
  - Exit positions in Blend, Aquarius, Soroswap and FxDAO. $12,000.
  - One interface for finding DeFi positions (Orion, then OctoPos, then direct on-chain reads as fallback), plus swap routing to XLM. $7,000.
  - Preview plan ("dry run"), multisig signing (accounts that need several signatures), and safety checks. $9,000.
- **Tranche 3: Mainnet (about week 16), $48,000.**
  - Production hardening and mainnet launch at a main domain. $14,000.
  - Full test suite, including random-input "fuzz" tests. $16,000.
  - Fixing findings from the Audit Bank security audit. The audit itself is paid separately. $10,000.
  - Documentation: user guide, integrator guide, architecture and security reference. $8,000.

## How it works

- **App.** A Next.js web app in TypeScript with a very small server. No database, no user accounts, no analytics.
- **Reads.** It reads account and contract state from Horizon (Stellar's public data API) and Soroban RPC (the smart-contract data service).
- **Signing.** Every transaction is signed in the user's own wallet, through Stellar Wallets Kit. The secret key never goes to the server.
- **Plan.** Soroban calls cannot share a transaction with classic operations. So a closure is "N+1" transactions: one Soroban transaction per DeFi exit, token drain, or allowance cancel, then one classic transaction (up to 100 operations) that removes everything else and merges. The tool re-reads the chain before each step.
- **Order of pieces.** An XState state machine (a strict step-by-step controller) runs: scan the account, build the plan, simulate each step, ask for confirmation, then execute.
- **Exchanges.** Exchanges do not accept `ACCOUNT_MERGE`. So the account merges into a short-lived "mediator" account, and the server co-signs one strictly checked 2-operation payment, with the exchange memo, to the exchange.
- **Safety.** A hard-coded list of allowed contracts is checked before each Soroban signature. There is also typed confirmation, high-value and scam-token warnings, and a fee cap.
- **Stellar features used:** classic operations (`ACCOUNT_MERGE`, `SET_OPTIONS`, path payments), Soroban contracts, SEP-41 tokens (the standard token interface), SEP-43 wallet signing, SEP-1 `stellar.toml`, and multisig coordination through Refractor or shared partial transactions.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recqvIs2iRu34ESGo | submission.md | Tranches, budgets, completion criteria |
| SCF project page | earlier SCF submission | https://communityfund.stellar.org/project/recESwsjmNII75BfE | — | Lists only this one SCF #44 submission |
| Technical Architecture Document (PDF) | architecture | https://drive.google.com/file/d/1qRNRr3kYomaQJ-0z9uIl2DmaiQE2OI1r/view?usp=sharing | architecture.pdf, architecture.txt | 20 sections: integrations, contracts, transaction model, mediator, threat model, milestones |
| Repo README | docs | https://github.com/bytemaster333/account-demolisher | repo-readme.md | Setup, routes, config |
| Architecture (repo docs) | architecture | https://github.com/bytemaster333/account-demolisher/blob/main/docs/content/docs/developers/concepts/architecture.mdx | docs-architecture.md | Module map, server routes, trust boundaries |
| Closure lifecycle (repo docs) | spec | https://github.com/bytemaster333/account-demolisher/blob/main/docs/content/docs/developers/concepts/closure-lifecycle.mdx | docs-closure-lifecycle.md | Audit, plan, simulate, execute |
| Mediator forward protocol (repo docs) | spec | https://github.com/bytemaster333/account-demolisher/blob/main/docs/content/docs/developers/protocol/mediator.mdx | docs-mediator.md | Exchange-merge endpoint and envelope rules |
| Security model (repo docs) | spec | https://github.com/bytemaster333/account-demolisher/blob/main/docs/content/docs/developers/security/model.mdx | docs-security-model.md | Trust and threat model |
| Security audit and remediation (repo docs) | audit | https://github.com/bytemaster333/account-demolisher/blob/main/docs/content/docs/developers/security/audit.mdx | docs-audit.md | Audit scope. External Audit Bank audit not yet reported there |
| Full docs source folder | docs site | https://github.com/bytemaster333/account-demolisher/tree/main/docs/content/docs | — | User guides, integrator guides, reference pages |
| Docs website | docs site | https://docs.demolisher.app | — | Unreachable on 2026-09-24 (server gave an empty reply) |
| Live app | demo | https://demolisher.app | — | Unreachable on 2026-09-24 (server gave an empty reply) |
| Prototype demo from submission | demo | https://demolisher.saliht.xyz/ | — | Unreachable on 2026-09-24 (same server) |
| stellar.expert/demolisher | docs | https://stellar.expert/demolisher | — | Older, classic-only tool this one extends |
| Stellar Command Insights (Soroban-ELK) | earlier SCF submission | https://github.com/bytemaster333/Soroban-ELK | — | Founder's SCF #29 project. A different product, not an earlier round of this one |
| Other founder repos | other | https://github.com/bytemaster333/Hashirama, https://github.com/bytemaster333/StylusVerify, https://github.com/bytemaster333/SentinelBag | — | Non-Stellar work cited as team background |

## Gaps

- The live app, docs site and demo (demolisher.app, docs.demolisher.app, demolisher.saliht.xyz) did not respond on 2026-09-24. The docs site's source is in the repo, and the key pages are saved here.
- No external audit report is published yet. The repo's audit page says the Audit Bank audit is still pending.
- No pitch deck, demo video or blog post is linked in the submission.
- The SCF project page shows no earlier SCF rounds for this project.
