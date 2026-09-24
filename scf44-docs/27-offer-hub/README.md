# Offer-Hub

Offer-Hub is a freelance marketplace on Stellar (a fast, low-fee blockchain). It is for freelancers in Latin America and the clients who hire them. A client's payment is locked in an escrow (money held by a program until the work is delivered). When the client approves the work, the money is released in USDC (a digital dollar). The freelancer can then cash it out to a local bank account. The team says fees are a flat 4%, against up to 25% on Upwork or Fiverr. SCF #44 award: $74.0K (Integration Track).

## What SCF #44 pays them to build

- **Tranche 0 (upfront):** $8,000 on award (per the architecture page).
- **Tranche 1, MVP: wallet connection and login ($16,000).** Connect Freighter, Lobstr and xBull wallets through Stellar Wallets Kit (SWK, a library that talks to many Stellar wallets) ($7,500). Log in by signing a one-time code with the wallet, next to the existing email login ($8,500). Target date on the architecture page: Sept 1, 2026.
- **Tranche 2, Testnet: signing in the browser and cash-out ($19,500).** The 4 escrow actions (create, release, refund, resolve dispute) are signed in the user's browser, not on the server ($6,000). BlindPay (a licensed payout company) turns USDC into local money in 7 countries: Mexico, Brazil, Colombia, Argentina, Peru, Chile, Costa Rica ($6,000). Automatic routing of payouts after a release ($3,500). Integration tests ($4,000). Target: Oct 20, 2026.
- **Tranche 3, Mainnet ($30,500).** Replace Horizon (Stellar's older data API) with Stellar RPC ($5,500). Launch on mainnet ($4,500). An automatic check that runs the full escrow-to-bank cycle and posts results to offer-hub.org/stats ($5,000). QA, monitoring and alerts ($10,000). Publish 3 open-source npm packages under MIT license: TrustlessWork adapter, SWK signing module, BlindPay adapter ($5,500). Target: Dec 5, 2026.

## How it works

- **Web app:** Next.js 15 frontend. The user's own wallet signs every escrow transaction through SWK. The server never holds the keys.
- **API:** a NestJS backend with PostgreSQL, Redis and BullMQ (a job queue). It handles login, orders, escrow, payments, webhooks and payouts.
- **Escrow on Stellar:** Offer-Hub does not write its own contracts. It uses TrustlessWork's audited Soroban contracts (Soroban is Stellar's smart-contract system). Payments are in USDC.
- **Payment flow:** the client creates an order. The API deploys an escrow. The client funds it from their wallet. The client later releases it. The API sees the release and sends a payout request to BlindPay, which pays the freelancer via SPEI, Pix, PSE and similar local bank systems.
- **Order states:** created → reserved → escrow funded → in progress → completed, or disputed → resolved.
- **Today (per the submission):** users have custodial wallets (the server holds their keys) with social login. A "Claim Your Wallet" flow will hand signing control to each user through Stellar's `set_options` operation.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recQJGuWgzvNmv2sM | submission.md | Tranches, costs, success checks |
| Technical architecture page | architecture | https://www.offer-hub.tech/architecture | architecture.md | Text copy. The diagrams are drawn in the browser, so their Mermaid source (diagram text) was added from the repo |
| SCF #44 demo video ("OFFER HUB") | demo | https://youtu.be/z1artVzYjIM | — | YouTube |
| Earlier submission, SCF #40 "Financial Access for Emerging Freelance" ($90K, Panel Review Failed) | earlier SCF submission | https://communityfund.stellar.org/submissions/recI2OLEC99fwhbzr | scf40-submission.md | Older plan: escrow marketplace, disputes, multi-currency payouts, reputation NFTs |
| SCF #40 pitch video ("OFFER HUB PITCH") | pitch | https://youtu.be/onFFyUSpih4 | — | YouTube |
| offer-hub-monorepo README | docs | https://github.com/OFFER-HUB/offer-hub-monorepo | repo-readme.md | Calls itself "OFFER-HUB Orchestrator": payments using Airtm + Trustless Work |
| Repo architecture overview | architecture | https://github.com/OFFER-HUB/offer-hub-monorepo/blob/HEAD/docs/architecture/overview.md | repo-architecture-overview.md | General stack doc. It says the backend is Express 5 and the database is "Planned", which does not match the NestJS stack in the submission. May be out of date |
| Repo payment flows | spec | https://github.com/OFFER-HUB/offer-hub-monorepo/blob/HEAD/docs/architecture/payment-flows.md | repo-payment-flows.md | Deposit, escrow, release and withdrawal steps and states. Describes server-managed wallets (the setup before SCF #44) |
| Frontend architecture | architecture | https://github.com/OFFER-HUB/OFFER-HUB-Frontend/blob/HEAD/docs/architecture.md | frontend-architecture.md | Next.js app. Mentions SWK signing in the browser |
| Docs site | docs site | https://www.offer-hub.tech/docs | — | Built from `content/docs` in offer-hub-monorepo (guides, API reference, SDK) |
| Older GitBook docs | docs site | https://offer-hub.gitbook.io/offer-hub/ | — | Linked from SCF #40. General overview, last updated about 10 months ago |
| GitHub org | code | https://github.com/OFFER-HUB | — | Repos: offer-hub-monorepo, OFFER-HUB (payments orchestrator), OFFER-HUB-Frontend, protocol-offer-hub |
| Live platform and stats | website | https://www.offer-hub.org , https://www.offer-hub.org/stats | — | Both open |
| Landing page | website | https://www.offer-hub.tech | — | Opens |

## Gaps

- No separate design or spec document for the SCF #44 work (SWK signing, BlindPay routing, RPC migration) beyond the architecture web page.
- The repo docs describe the older custodial and Airtm setups, not the planned SWK + BlindPay design.
- The architecture page diagrams exist only as Mermaid text in the repo (copied into architecture.md), not as images.
- The LinkedIn page (https://www.linkedin.com/in/offer-hub-bb797a389) blocks automated access (HTTP 999). Not checked.
- The SCF #40 link https://github.com/OFFER-HUB/offer-hub now redirects to https://github.com/OFFER-HUB/OFFER-HUB (the payments orchestrator repo).
