# Muney App

Muney is a business-to-business (B2B) payment company in Latin America. It lets people turn stablecoins (crypto tokens pegged to the US dollar, here USDC) into cash, and cash into stablecoins, at local shops. For SCF #44 it adds this cash-in / cash-out service on Stellar for El Salvador. It is for wallets, fintech apps and money-transfer companies ("partners") that want to offer cash pickup and cash deposit without building their own shop network. Shops ("merchants") hand out or accept the cash; USDC on Stellar moves the money digitally. The plan starts with a 100-merchant pilot, growing toward 500.

## What SCF #44 pays them to build

Total award: $95,000. The proposal lists three tranches (a "tranche" is one payment tied to deliverables); the repo roadmap also mentions a "Tranche 0" of 10% on award acceptance.

- **Tranche 1 — MVP / Stellar USDC testnet adapter, $19,000 (20%)**
  - A USDC adapter on Stellar testnet (test network) and a partner API that can pick `stellar_usdc` as the settlement rail.
  - Transaction state machine (the fixed list of steps an order moves through), webhooks (automatic messages to partners), dashboard view, reconciliation export, idempotency and retry, recorded demo, open-source adapter v0.
- **Tranche 2 — Anchor Platform + SDP integration, $28,500 (30%)**
  - Anchor Platform ($10,500): Stellar's standard server for deposits and withdrawals (SEP-6 / SEP-24 / SEP-31, where applicable).
  - Stellar Disbursement Platform, SDP ($9,500): Stellar's tool for sending many payouts at once.
  - Compliance logging and reconciliation ($2,500), partner sandbox and pilot flows ($2,500), tests ($2,000), open-source examples and docs ($1,500). At least two pilot partners (or simulations) complete testnet flows.
- **Tranche 3 — Mainnet launch + partner pilot, $38,000 (40%)**
  - USDC on Stellar mainnet (the live network) with monitoring, alerting and production reconciliation.
  - Pilot with 100 merchants and at least two B2B partner flows; integration with 2 wallets or money transmitters.
  - Final developer docs, integration cookbook, public demo page, first metrics.

## How it works

- **Partner side:** the partner's app uses the **User SDK** (a code library) to create cash-in / cash-out orders, get quotes, find a merchant and track status. The end user never touches the blockchain directly.
- **Muney core (private code):** API gateway, orchestration engine (runs the order state machine), merchant routing (picks a shop with enough cash), compliance engine (KYC = identity checks, KYT = transaction checks, sanctions screening), quote and fee engine, ledger and reconciliation, signed webhooks.
- **Stellar integration layer (the SCF scope, open source):**
  - **Stellar USDC Settlement Adapter:** the only part that talks to Horizon (Stellar's API). It detects incoming USDC payments by memo (a reference note on the payment), builds and submits payments, and waits for confirmation (~5 seconds).
  - **Secure Signing Service:** the only part that holds private keys. Keys are never in the SDKs or the adapter.
  - **Anchor Platform:** SEP-10 login and SEP-24 deposit/withdraw, so standard Stellar wallets can use Muney. SEP-31 only if a partner needs it.
  - **Stellar Disbursement Platform:** bulk payouts for remittances or payroll.
- **Merchant side:** the **Merchant SDK** runs on a shop's phone, tablet or POS. The merchant scans the user's QR code, hands over or receives cash, and confirms.
- **Cash-out flow in short:** user asks to cash out → compliance check → a merchant is chosen → the wallet sends USDC on Stellar → adapter confirms it → user goes to the shop, shows the QR code, gets cash → ledger and partner are updated. Cash-in is the reverse (cash first, then Muney sends USDC).
- Stellar features used: USDC (a Stellar classic asset), memos, Anchor Platform (SEP-1/10/24, SEP-6/31 evaluated), SDP. No Soroban smart contracts in scope; a repo note weighs moving rules to Soroban later.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recnd9hYmEgcp2vB2 | submission.md | Official proposal, $95K |
| SCF project page | earlier SCF submission | https://communityfund.stellar.org/project/rec1JrrzqsFsYBnCM | — | Lists only SCF #44; no earlier rounds |
| Architecture diagrams (PDF) | architecture | https://drive.google.com/file/d/1MswA_78S2yarWKlkW0336TOtoKgzOcGn/view?usp=sharing | architecture.pdf, architecture.md | Image-only; transcribed to text |
| Repo architecture doc | architecture | https://gitlab.com/holamuney/muney-stellar-integration/-/blob/main/docs/architecture.md | repo-architecture.md | Text version of the diagrams (v1.1) |
| Repo README | docs site | https://gitlab.com/holamuney/muney-stellar-integration | repo-readme.md | Open-source parts; core engine stays private |
| Roadmap by tranche | requirements | https://gitlab.com/holamuney/muney-stellar-integration/-/blob/main/docs/roadmap-by-tranche.md | roadmap-by-tranche.md | Checklist with progress and testnet tx hashes |
| Partner integration cookbook | spec | https://gitlab.com/holamuney/muney-stellar-integration/-/blob/main/docs/partner-cookbook.md | partner-cookbook.md | How partners integrate (SDK, SEP-24, SDP) |
| Transaction state machine | spec | https://gitlab.com/holamuney/muney-stellar-integration/-/blob/main/docs/transaction-state-machine.md | transaction-state-machine.md | |
| Anchor SEP-24 bridge | spec | https://gitlab.com/holamuney/muney-stellar-integration/-/blob/main/docs/anchor-sep24-bridge.md | anchor-sep24-bridge.md | |
| Soroban business rules note | architecture | https://gitlab.com/holamuney/muney-stellar-integration/-/blob/main/docs/soroban-business-rules.md | soroban-business-rules.md | Pros/cons of moving rules on-chain |
| Webhooks doc | spec | https://gitlab.com/holamuney/muney-stellar-integration/-/blob/main/docs/webhooks.md | — | |
| Diagrams (PNG) | architecture | https://gitlab.com/holamuney/muney-stellar-integration/-/tree/main/diagrams | — | Same diagrams as the PDF |
| Website | docs site | https://muney.app | — | Page loads but shows almost no text to a fetcher |
| Sandbox API / Anchor / SDP | demo | https://devapi.muney.cc, https://devanchor.muney.cc, https://devsdp.muney.cc | — | Testnet services named in the cookbook; all respond |
| Traction evidence file | pitch | https://drive.google.com/file/d/1o_p4-f4M8hrt7fTAaayZz8hqn6mQ8u9-/view?usp=drive_link | — | HTTP 404: private or deleted |

## Gaps

- The second Google Drive file in the submission (the traction "Link", about $3.5M volume) returns 404; could not open.
- The submission says "more information about development time for each integration partner here" and "list on maps", but those links were not captured in the submission text.
- The recorded demo video named in the roadmap has no public link found.
- No pitch deck, whitepaper or audit found. Core engine code is private by design.
- The empty `architecture.txt` (PDF had no text layer) was replaced by the transcription in `architecture.md`.
