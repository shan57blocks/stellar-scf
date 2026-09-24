# LumenWipe

LumenWipe is a free, open-source web tool that closes a Stellar account and gets back the XLM locked inside it. On Stellar, every account must keep a minimum XLM balance (a "reserve"), and every extra item it holds (a trustline, which is permission to hold a token; an open trade offer; a data entry; an extra signer) locks 0.5 XLM more. To close an account you must remove all of these in the right order and then "merge" it (send everything left to another account). LumenWipe does this step by step. It is for individual users, wallets, exchanges and platforms that create many accounts for their users (its first partner, Pollar, signed a letter of intent). It answers the SCF "Account Demolisher" RFP (a Request for Proposals, where SCF asks teams to build a specific tool). It is non-custodial: your secret keys never leave your browser.

SCF #44 award: $120,000, Build award, Developer Tooling category.

## What SCF #44 pays them to build

- **Tranche 1 — Classic completion (MVP), $24,000**
  - Claimable balances (funds sent to you that you must claim), end to end — $7,500
  - Per-asset choice: convert, transfer, or burn (send back to the issuer) each token — $5,500
  - Swappable account-state data source, with a check that no item was missed — $4,500
  - Multisig (accounts that need several signatures) closing with signatures from several wallets — $6,500
- **Tranche 2 — Soroban and DeFi on testnet, $36,000**
  - Detect DeFi positions through OctoPos or Orion (DeFi position data services), with direct contract reads as fallback — $5,000
  - Exit positions in Blend, Aquarius and Soroswap — $12,000
  - Convert Soroban tokens, an allowance inspector (lists and revokes spending permissions given to apps), and sponsored fees for accounts too empty to pay fees — $10,000
  - Hostile-account test suite, a STRIDE threat model (a standard checklist of attack types), and a security tooling run — $9,000
- **Tranche 3 — Production launch on mainnet, $48,000**
  - Security hardening, an independent review via the SDF Audit Bank, and mainnet launch with monitoring — $20,000
  - Exit positions in Phoenix and FxDAO — $6,000
  - Public REST API and TypeScript SDK (npm package) — $14,000
  - UX from five user-testing sessions, plain-language errors, full docs — $8,000

## How it works

- **Three parts.** An API service builds every transaction but leaves it unsigned. The browser app checks each transaction against what the user chose (a `verify()` step) and signs it locally. The Stellar network and outside data services sit behind the API.
- **Data.** Stellar RPC (the network's main read/submit service) is used where it can. Listing everything an account holds needs an indexer, so it reads that from one Horizon-compatible endpoint, set by config. They run no indexer of their own.
- **The plan.** From the account's live state it builds a fixed, ordered plan: exit DeFi positions, remove extra signers, remove data entries, claim balances, cancel offers, leave liquidity pools, handle each token, remove trustlines, merge. Same state gives the same plan. The user reviews it before signing anything.
- **Soroban and DeFi.** Each protocol exit (Blend, Aquarius, Soroswap, Phoenix, FxDAO) is one Soroban contract call per transaction, simulated before signing. Known contracts are listed in a registry by code hash (`wasmHash`). An unknown contract is flagged for manual review. Token swaps route through the Soroswap API.
- **Exchanges.** Exchanges do not accept a merge. So the account is merged into one shared "mediator" account, which forwards the XLM with the required memo, all in one transaction. The API co-signs only that forwarding payment. A registry of exchange addresses decides when a memo is required.
- **Sponsored fees.** Fee-bump transactions (CAP-15: a second account pays the fee) let accounts stuck at the minimum balance still close.
- **Stellar standards used:** SEP-41 (Soroban token standard), SEP-43 / stellar-wallets-kit (wallet connection), CAP-15 fee bumps.
- **Deployment (per the current architecture doc).** Bun monorepo with `apps/web` (Next.js on Vercel), `apps/api` (NestJS container on Google Cloud Run) and `packages/sdk`. The submission instead described Vercel serverless routes and Vercel KV; the later docs changed this.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recuKWaSdUL8Lkw9o | submission.md | Tranches, budget, completion criteria |
| Technical architecture | architecture | https://docs.lumenwipe.com/architecture | architecture.md | 25 sections; diagrams are remote images |
| Executive summary | pitch | https://docs.lumenwipe.com/executive-summary | executive-summary.md | One-page overview |
| STRIDE threat model | spec | https://docs.lumenwipe.com/threat-model | threat-model.md | Tranche 2 deliverable |
| Security tooling remediation plan | audit | https://docs.lumenwipe.com/security-remediation-plan | security-remediation-plan.md | Internal tooling run, not the independent review |
| On-chain monitoring plan | spec | https://docs.lumenwipe.com/monitoring-plan | monitoring-plan.md | |
| Community and maintenance | docs site | https://docs.lumenwipe.com/community-and-communications | community-and-communications.md | Update cadence, channels |
| Use cases overview | docs site | https://docs.lumenwipe.com/use-cases/overview | use-cases.md | Sub-pages for individuals, wallets, embedded wallets, businesses not saved |
| Docs site home | docs site | https://docs.lumenwipe.com | — | Page index: https://docs.lumenwipe.com/llms.txt |
| Repo README | docs site | https://github.com/LumenWipe/lumenwipe | repo-readme.md | Apache-2.0; shows T1 "In progress", T2/T3 "Planned". Repo `docs/` folder mirrors the docs site |
| SDK README | spec | https://github.com/LumenWipe/lumenwipe/blob/main/packages/sdk/README.md | sdk-readme.md | `@lumenwipe/sdk` 0.2.0 on npm (https://registry.npmjs.org/@lumenwipe/sdk) |
| Pollar letter of intent | pitch | https://drive.google.com/file/d/1VrRXZOwgN51TRduPM2lzI9Hbz5ooDovF/view | — | Public PDF, 2 pages, non-binding; Pollar to start integration within 60 days of SDK/API availability |
| Reference demolisher (StellarExpert / Orbit Lens) | demo | https://stellar.expert/demolisher/public | — | Existing tool the design studied; loads in browser only |
| Example mainnet closure | demo | https://stellar.expert/explorer/public/tx/270677660857139200 | — | Traction evidence |
| Testnet E2E test account | demo | https://stellar.expert/explorer/testnet/account/GALYU2WHUH5HV3BAYTOP5ZMUJWXF43QLSVFTEMBYCWF6MM5JDFHXFESQ | — | Playwright tests run against it |
| Web app | docs site | https://www.lumenwipe.com | — | |
| SCF project page | earlier SCF submission | https://communityfund.stellar.org/project/recXWUShn5Dxsku2x | — | Lists only this SCF #44 submission; no earlier LumenWipe rounds |
| Community: Discord / Matrix | blog | https://discord.gg/hDCNaW6xn , https://matrix.to/#/#lumenwipe:matrix.org | — | Monthly updates promised here |

## Gaps

- The SCF "Account Demolisher" RFP text itself is not linked in the submission; not found.
- The "LumenWipe blog" and the public umami analytics link are mentioned in the submission, but no URL was given.
- No independent security audit yet (planned in Tranche 3 via SDF Audit Bank).
- The team's other past SCF awards (Chaincerts, Elixir Stellar SDK) are separate projects, not earlier LumenWipe requirements; not collected.
- npmjs.com page blocks scripted access (403); the npm registry API confirms the package exists.
