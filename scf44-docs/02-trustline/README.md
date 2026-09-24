# Trustline

Trustline is a safety check that runs before a blockchain transaction. A protocol, wallet or vault tells Trustline what a user wants to do (the "intent"). Trustline's server checks it against rules the protocol set: identity checks, sanctions lists, wallet risk, location, one-time codes, and so on. If the intent passes, Trustline writes a short-lived "proof" on-chain. The protected smart contract (a program that runs on the blockchain) only runs the action if a matching proof exists. The goal is to make on-chain services safe and auditable enough for institutions and insurers. It already runs on EVM chains (Ethereum-style chains); this grant brings it to Stellar. It is for institutions, asset managers and Stellar builders who need compliance controls.

## What SCF #44 pays them to build

Total: $133.6K (category: Developer Tooling).

- **Tranche 1 – MVP ($44,500)**
  - Soroban ValidationEngine contract that stores, checks and "consumes" (uses up) proofs — $17,800
  - Backend module (Kotlin) that publishes proofs to Stellar through Soroban RPC (the API used to talk to Stellar smart contracts) — $16,500
  - Rust SDK (code library) so Soroban contracts can require a proof, plus a "Trustline Firewall" contract — $10,200
- **Tranche 2 – Testnet ($66,800)**
  - TypeScript web SDK for frontends to build the intent and ask for pre-validation — $24,400
  - End-to-end testnet demo, preferably a Trustline-protected SEP-56 tokenized vault (a Stellar standard for vaults that issue shares), with allow/deny cases and a runbook — $19,300
  - Onboarding website/API so Stellar projects can register and set their rules — $23,100
- **Tranche 3 – Mainnet ($22,300)**
  - ValidationEngine on mainnet, backend publishing to mainnet with monitoring — $12,100
  - Developer docs and at least one full example project — $10,200

## How it works

- **Frontend (the app's web page)** collects the intent (contract, method, arguments, user address) and calls the Trustline backend. The user may need to pass a one-time code or other step.
- **Trustline backend** (Kotlin/Ktor) checks the intent against the project's rules, using outside providers (identity, sanctions, wallet risk). If approved, it signs and sends a transaction that records a proof in the ValidationEngine. The signing key will be kept in Google Cloud KMS or DFNS (key-custody services).
- **ValidationEngine** (Soroban contract, Rust) stores proofs keyed by an "intent hash" (a fingerprint of the exact call: network, contract, method, arguments, caller, expiry, nonce). Proofs live in Soroban temporary storage, so they expire on their own. Each proof can be used once.
- **Protected contract**: the user signs and sends the real transaction from their own wallet. The contract calls the ValidationEngine; the action only runs if a matching, unexpired, unused proof exists. Two ways to protect a contract:
  - Direct: the contract calls `require_trustline(...)` from the Rust SDK.
  - Firewall: for contracts already deployed, the Trustline Firewall becomes the contract's admin and forwards calls only after a proof check. No change to the original code.
- In the delivered code, the engine is split into a shared **Trustline Registry** (list of allowed proof publishers and lookups), a per-client **Validation Engine instance**, and an optional **sanctions list** contract.
- **Stellar features used**: Soroban contracts and temporary storage, Soroban RPC with `simulateTransaction`, Soroban `require_auth` (Trustline adds a second check on top of it), SEP-10/SEP-45 (Stellar login standards) as inputs to the rules, and SEP-56 vaults as the reference use case. Anchors (SEP-6/24/31) are named as a future option only.
- The system "fails closed": no proof, expired proof, reused proof or mismatched call means the action does not run. Trustline never holds user funds or signs user transactions.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recv4jiNRbePUJRQI | submission.md | Awarded $133.6K |
| Technical Architecture for Stellar | architecture | https://docs.google.com/document/d/1ZL23jXFAD-ZXA1-3iFa6rhqQmABJvZRnk1-yzuStYoc | architecture.txt | Diagrams are images and are not in the text copy |
| SCF #43 submission (earlier round) | earlier SCF submission | https://communityfund.stellar.org/submissions/rectRK7jcM7NXvzwy | scf43-submission.md | Not awarded. Same budget and tranches. Promised Soroswap and Blend integrations instead of the SEP-56 vault |
| SCF project page | earlier SCF submission | https://communityfund.stellar.org/project/rec0erhiIkQUjS23Z | — | Lists only SCF #43 and SCF #44 |
| Project analytics / traction | pitch | https://docs.google.com/document/d/18RCoOCcVhBeG_NYCV_4ONlqiRheTAUPsimbKPwwG6vs | analytics.md | Pilot with DFI-Labs, XRPL demo day, links |
| stellar-sdk README (Rust SDK) | spec | https://github.com/TrustLine-id/stellar-sdk | stellar-sdk-readme.md | Integration guide and API reference |
| stellar-validation-engine README | spec | https://github.com/TrustLine-id/stellar-validation-engine | validation-engine-readme.md | Contract layout and functions; says not externally audited yet |
| stellar-validation-engine SECURITY.md | spec | https://github.com/TrustLine-id/stellar-validation-engine/blob/HEAD/SECURITY.md | — | Trust model, key custody, replay protection |
| stellar-demo-app README | docs site | https://github.com/TrustLine-id/stellar-demo-app | demo-app-readme.md | SCF #44 testnet demo: Firewall and Payment Forwarder tabs |
| Backend pre-validation API | spec | https://github.com/TrustLine-id/stellar-demo-app/blob/HEAD/BACKEND_PREVALIDATION_API.md | prevalidation-api.md | JSON-RPC `openSession` / `validate` |
| Earlier proof of concept repo | demo | https://github.com/TrustLine-id/stellar-poc-registry-escrow | — | Registry and escrow PoC (README, DEPLOY.md) |
| PoC escrow app | demo | https://stellar-poc-escrow.trustline.id/ | — | JavaScript app |
| PoC registry app | demo | https://stellar-poc-registry.trustline.id/ | — | JavaScript app |
| "Stellar x Trustline POC" video | demo | https://www.youtube.com/watch?v=ZJkyTd0-CXo | — | YouTube |
| "Short Pitch Demo Stellar" video | pitch | https://youtu.be/5POocPP5HVA | — | Video on the SCF submission page |
| Builder portal | docs site | https://dev.trustline.id/ | — | JavaScript app, no readable static text |
| Onboarding site | demo | https://onboarding.trustline.id/ | — | JavaScript app |
| LOI – DFI-Labs | pitch | https://drive.google.com/file/d/1o_QoWNz7hKiSSgF0VURbab6fIP-8ld6e/view | — | Letter of intent, 3-page PDF |
| LOI – Cushion (SCF #42) | pitch | https://drive.google.com/file/d/11FAsGBUmtneXtvoBI6EQgJ1oRUM12P-w/view | — | Letter of intent, 2-page PDF |
| Public LOIs folder | pitch | https://drive.google.com/drive/folders/1BdlS5Dqhye-8DAfLLFLEwPAEz-eVUKxZ | — | Shows "LOI - Dfi-Labs.pdf" |
| Website | docs site | https://www.trustline.id | — | Marketing site |
| Benjamin Simatos on Medium | blog | https://medium.com/@benjamin_69123 | — | Page blocks scripts (HTTP 403); the RSS feed only has 2019–2020 posts about IOV/Starname, nothing on Trustline |

## Gaps

- The architecture diagrams are images inside the Google Doc. The text copy does not include them.
- No separate web SDK repo for Stellar found yet. The existing `websdk` repo was last updated in February 2026, before this grant.
- No public repo for the Kotlin backend or the onboarding site. Only their live apps and the API reference are public.
- No SEP-56 vault reference contract is visible in the public repos yet. The demo uses a counter and a payment forwarder.
- No mainnet contract addresses published yet. No external audit.
