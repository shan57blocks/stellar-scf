# TroqPay: Sell with Pix. Receive in USD.

TroqPay is a live payments product for registered companies in Brazil. Merchants take payments by Pix (Brazil's instant bank-transfer system) through an API, a hosted checkout page, payment links and point-of-sale. TroqPay keeps a balance in BRL (Brazilian reais) for each merchant. Today merchants can cash out by Pix or as USDT (a dollar stablecoin, a crypto token worth one US dollar). The SCF #44 grant ($60K, Integration Track) moves the stablecoin cash-out to native USDC on Stellar, held in a wallet the merchant controls, with optional yield through DeFindex.

## What SCF #44 pays them to build

Total $60,000, paid 10/20/30/40%. PaltaLabs (an outside Stellar dev shop) builds the first version; TroqPay builds the rest.

- **#0 Kickoff — $6,000.** PaltaLabs coordination, sandbox and branch setup.
- **Tranche 1: standalone MVP — $12,000 (owner: PaltaLabs).** A separate demo app, outside TroqPay's product: merchant account, Privy wallet (Privy = a login service that creates a wallet without seed phrases), BRL-to-USDC on-ramp through Alfred or another licensed provider, DeFindex deposit and withdraw, off-ramp back to BRL. Also a decision note on how to sign DeFindex transactions, a hand-off document, a recorded demo and a public signing reference.
- **Tranche 2: testnet integration — $18,000 (owner: TroqPay, about 300 hours).** Add a `USDC_STELLAR` payout rail inside TroqPay behind an off-by-default feature flag. Privy onboarding in the app, account and USDC trustline setup (a trustline = a Stellar account's opt-in to hold a token), quotes and checks, testnet on-ramp, DeFindex on testnet, dashboard states and a reconciliation report.
- **Tranche 3: capped mainnet launch — $24,000 (owner: TroqPay, about 420 hours).** Turn it on for approved merchants only, with caps and a kill switch. At least one real Pix-to-USDC mainnet settlement, a transaction watcher, scheduled reconciliation and proof-of-funds dashboard, DeFindex on mainnet, failure recovery, monitoring, UX readiness, a go-live metrics report, and launch and rollback runbooks.

PaltaLabs gets $20,000 total ($18,000 for the MVP plus $2,000 for 16 hours of support later). TroqPay engineering gets $40,000.

## How it works

- **Collecting money (exists today).** A buyer pays a Pix charge. The Pix provider tells TroqPay by webhook. TroqPay credits the merchant's BRL ledger. The stack is Next.js with Prisma and PostgreSQL.
- **Pix to USDC on Stellar (new).** The merchant asks to settle part of their balance. TroqPay debits the BRL ledger and tells a licensed Brazilian exchange partner (a "VASP" or "anchor", a regulated company that swaps local money for crypto) to convert it. The partner sends native USDC on Stellar to the merchant's wallet. TroqPay stores the Stellar transaction hash and the partner's reference, then reconciles.
- **USDC back to BRL (new).** The merchant signs a Stellar payment of USDC to the partner. The partner pays BRL back by Pix.
- **Optional yield (new).** The merchant signs a deposit into a DeFindex vault (DeFindex = an existing, audited Soroban smart-contract yield product by PaltaLabs). TroqPay reads the position with the `@defindex/sdk`.
- **Who holds keys.** The merchant signs everything from their own Privy wallet. TroqPay never holds keys and never converts money itself. Fallback if Privy cannot sign Soroban calls well: a Stellar passkey smart wallet.
- **Stellar features used:** native Circle USDC, trustlines, Horizon/RPC for sending and reading transactions, Soroban contract calls (DeFindex), and SEP flows (Stellar standard protocols for anchors) where the partner supports them. No new smart contracts are written.
- **New data records:** settlement records, a wallet registry, and vault positions, all tied to the existing BRL ledger.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recidiiEnQNycc3LA | submission.md | Tranches and completion criteria |
| SCF project page | earlier SCF submission | https://communityfund.stellar.org/project/recEog2l3dGpvLqwJ | — | Lists only this SCF #44 submission; no earlier rounds |
| Technical Architecture (revised 14 June 2026) | architecture | https://docs.google.com/document/d/1EQa00t9Hmn3KvYZCTfonQgaTiQ5euqTi/edit | architecture.txt, architecture.docx | Figures (component diagram, flow diagram, delivery plan) are only in the .docx |
| Pitch deck, Web Summit Rio 2026 | pitch | https://drive.google.com/file/d/1PntPBYAJAFpx4VqRyJIK7GUjG8CepA2l/view | pitch-deck.md | Transcribed from slide images; describes the current product (USDT/BRL payouts), not the Stellar plan |
| Evidence folder (OTC volume screenshots, Web Summit videos, pitch deck) | demo | https://drive.google.com/drive/folders/1lqU6xWZp2AJaK6fVubzF9IQzVni3gRD5 | — | Public. Holds PNG screenshots (TPV, orders, AML monitoring), two .MOV videos and a 60s .mp4; not copied |
| API docs (docs site) | docs site | https://docs.troqpay.com | api-docs.md | Copy of https://docs.troqpay.com/llms.txt (Portuguese). Covers today's API only; no Stellar/USDC rail yet |
| GitHub org profile | docs site | https://github.com/troqpay/.github/blob/main/profile/README.md | org-profile.md | Product overview, links to SDK, agent plugin, docs |
| JavaScript/TypeScript SDK | spec | https://github.com/troqpay/sdk | — | Client for today's API; README only |
| Agent plugin (MCP server) | spec | https://github.com/troqpay/agent-plugin | — | MCP tools for checkouts and balance |
| Website | docs site | https://troqpay.com | — | Marketing site, has a blog at /blog |
| Production app | demo | https://app.troqpay.com/ | — | Login required |
| Web Summit Rio 2026 appearance | blog | https://rio.websummit.com/pt-br/appearances/rio26/58152bfd-2a4a-4e50-96c9-37ee89270fe2/troqpay/ | — | Opens (HTTP 200) |
| Founder YouTube channel | demo | https://www.youtube.com/@ovitorpio | — | Social proof, not product docs |
| Instagram / LinkedIn | blog | https://www.instagram.com/troqpay/ , https://www.linkedin.com/company/troqpay/ | — | Social pages; may require login to view |

## Gaps

- The Stellar work is not public yet. The SDK, agent plugin and docs site cover only Pix and USDT payouts. No public repo for the Tranche 1 MVP or the "public signing reference" was found.
- The core TroqPay product code (where the `USDC_STELLAR` rail will live) is private.
- The architecture diagrams are images inside architecture.docx; architecture.txt has only their captions.
- One slide of the 13-slide pitch deck was not readable.
- No earlier SCF submissions exist for this project.
