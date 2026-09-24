# VERSO — Peru's First Regulated Stellar Anchor

VERSO is a licensed crypto exchange in Peru (a "VASP", meaning a company the regulator allows to exchange crypto). It has run since 2022. With this grant it becomes a Stellar "anchor": a service that swaps local money for digital dollars on Stellar and back. Peruvian users can pay Peruvian soles (PEN) or US dollars by bank transfer and get USDC (a digital dollar) in any Stellar wallet, like Lobstr or Freighter. They can also send USDC and get soles or dollars in their Peruvian bank account.

## What SCF #44 pays them to build

Total award: $110,000. The architecture doc adds a 10% upfront payment (T0, $11,000) for setup.

- **Tranche 1 — MVP on testnet ($22,000).** Testnet means Stellar's practice network with fake money.
  - SEP-1 ($9,000): publish a `stellar.toml` file so wallets can find VERSO.
  - SEP-10 ($8,000): users log in by signing with their Stellar wallet, linked to VERSO's KYC (identity checks).
  - First simulated deposit on testnet, soles in, USDC out ($5,000).
- **Tranche 2 — Full testnet ($33,000).**
  - SEP-24 in-wallet deposit and withdraw screens, 10+ test transactions ($16,000).
  - SEP-38 live PEN/USDC price quotes inside wallets ($9,000).
  - A service that watches the wallet in real time and checks it against VERSO's books. It must run 14 days with no unresolved mismatch ($8,000).
- **Tranche 3 — Mainnet, real money ($44,000).**
  - All four SEPs live on mainnet ($15,000).
  - Semi-automatic bank settlement, averaging 30 minutes or less ($12,000).
  - 10+ real transactions visible on Stellar Expert (a public Stellar explorer), plus a listing request to the Stellar Anchor Directory ($3,000).
  - Security hardening and audit preparation ($8,000).
  - Public technical documentation ($6,000).

## How it works

- **The plan (architecture doc).** Run the Stellar Development Foundation's (SDF) ready-made "Anchor Platform" in front of VERSO's existing systems. SEPs are Stellar's shared standards for wallets and anchors. The platform handles the SEP steps. For business decisions, it calls VERSO's backend through four "callbacks" (web requests VERSO answers):
  - deposit: returns the bank transfer details;
  - withdraw: returns VERSO's Stellar wallet address;
  - transaction update: runs compliance checks and pays out in PEN;
  - rate: returns the PEN/USDC price.
- **VERSO's existing systems.**
  - Client portal: versotek.io, built in Django (a Python web framework).
  - BASE_DE_CLIENTES: the internal compliance and accounting system.
  - Identity checks: DIDIT does KYC; OpenSanctions checks sanctions lists.
  - Banks: BCP and Interbank, over Peru's CCI/CCE interbank network.
- **Money flow.** Deposit: the user sends soles by bank transfer. An operator confirms it, then USDC goes out from VERSO's "hot wallet" (the online wallet it pays from). Withdraw: the user sends USDC to VERSO, and VERSO pays soles to their bank.
- **Keys and records.** The plan uses AWS KMS for signing keys, meaning keys are kept in Amazon's key vault and never stored as plain text. A second, offline "cold wallet" needs two approvals to move funds. A Horizon streaming service (Horizon is Stellar's data API) records every USDC movement in PostgreSQL and raises an alert on any mismatch.
- **What was actually built for Tranche 1 (repo README).** It differs from the plan in three ways:
  - It uses **django-polaris**, SDF's Python anchor library, inside VERSO's Django app, not the Anchor Platform container.
  - It has **no AWS KMS**. The README says AWS KMS cannot sign with Ed25519, the key type Stellar uses. The signing key is an environment variable on Railway (the hosting service) for now.
  - The **KYC check is moved to Tranche 2**, inside the SEP-24 screens.
- **Live testnet anchor:** anchor.versotek.io.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/rec4pEX17tlzuaaex | submission.md | Only submission on the project page, so no earlier rounds |
| Technical Architecture Document | architecture | https://github.com/WildorAP/verso-tad/blob/main/technical-architecture-document.md | architecture.md | Diagrams are SVG files in the repo's `assets/` folder, not copied |
| verso-anchor repo (same document plus SVG diagrams) | architecture | https://github.com/WildorAP/verso-anchor | — | Earlier copy of the same document |
| VERSO-SDF repo README | docs / status report | https://github.com/WildorAP/VERSO-SDF | verso-sdf-readme.md | Tranche 1 status, test steps, and the three changes from the plan |
| Live testnet anchor | demo | https://anchor.versotek.io/ | — | Status page |
| stellar.toml (SEP-1) | spec | https://anchor.versotek.io/.well-known/stellar.toml | — | Live |
| Supporting documents folder (Google Drive) | evidence | https://drive.google.com/drive/folders/1xMgAN0pElm_O9-q7UpSMlqdvelq7kr0X | — | Public. UTEC Ventures letter (PNG), SUNAT tax filings 2022–2025 (PDF), SBS VASP license (PDF). Business proof, not design docs, so not copied |
| Website | website | https://www.versotek.io/ | — | Main exchange website |

## Gaps

- No pitch deck, whitepaper, audit, or demo video is linked.
- The architecture doc's diagrams are image files. Only their references are in `architecture.md`.
- The architecture doc still describes the Anchor Platform and AWS KMS. The code uses django-polaris and no KMS. Only the repo README explains this change.
