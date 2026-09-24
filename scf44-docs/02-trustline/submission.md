# Trustline: Unlock Onchain Finance for Institutions

Source: https://communityfund.stellar.org/submissions/recv4jiNRbePUJRQI (SCF #44, awarded $133.6K, category: Developer Tooling)

- Website: https://www.trustline.id
- Architecture doc: https://docs.google.com/document/d/1ZL23jXFAD-ZXA1-3iFa6rhqQmABJvZRnk1-yzuStYoc

## Links in the submission

- https://docs.google.com/document/d/1ZL23jXFAD-ZXA1-3iFa6rhqQmABJvZRnk1-yzuStYoc
- https://www.youtube.com/watch?v=ZJkyTd0-CXo
- https://github.com/TrustLine-id/stellar-poc-registry-escrow
- https://docs.google.com/document/d/18RCoOCcVhBeG_NYCV_4ONlqiRheTAUPsimbKPwwG6vs
- https://drive.google.com/file/d/1o_QoWNz7hKiSSgF0VURbab6fIP-8ld6e/view?usp=drive_link
- https://drive.google.com/file/d/11FAsGBUmtneXtvoBI6EQgJ1oRUM12P-w/view?usp=sharing
- https://drive.google.com/drive/folders/1BdlS5Dqhye-8DAfLLFLEwPAEz-eVUKxZ?usp=sharing
- https://medium.com/@benjamin_69123

## Submission text (as published)

```text
Products & Services
TradFi is going onchain, and trillions of dollars of institutional capital are following. But hacks like ByBit, Drift, and KelpDAO are leaving investors exposed to losses that traditional markets would never tolerate. Trustline closes that gap, securing and distributing insurance coverage purpose-built for onchain services, with a proven deployment on EVM chains and now coming on Stellar.
This project adds native Stellar support to Trustline by implementing:
A
Soroban-native ValidationEngine contract
 to store and serve validation proofs on Stellar.
A
backend publishing module
 that writes validation proofs to the Stellar network via Soroban RPC.
A
Soroban contract SDK
 (Rust crate) to make Stellar contracts “Trustlined” with minimal code changes.
A
TypeScript web SDK layer
 to orchestrate pre-validation and Stellar wallet transaction flows.
An
open-source example web application
 showcasing the complete user experience and implementation of the Trustline Stellar validation flow.
A
Trustline-protected SEP-56-compatible institutional tokenized vault
 reference implementation, demonstrating how standard vault operations and privileged vault-management actions can be gated by pre-transaction validation and onchain proof consumption.
An
adapter-ready vault architecture
 designed to support pluggable yield strategies over time. Stellar DeFi primitives such as Blend and Soroswap are natural first candidates for future reference adapters and compatibility layers, enabling end-to-end permissioned DeFi workflows once the core Trustline infrastructure is in place.
Once completed, Stellar users/builders will be able to:
Integrate Trustline validation into Soroban smart contracts and enforce “validated intents only.”
Trigger the Trustline pre-validation flow from Stellar-enabled frontends.
Produce onchain validation evidence on Stellar (events + storage) that can be audited and relied on by contracts.
Build Stellar applications (e.g., token issuers, vaults, governance, DeFi primitives) with a configurable compliance/trust gate, similar to the existing EVM integration.
Seamlessly integrate with existing Stellar DeFi protocols to create fully permissioned trading and yield strategies.
The goal is not only to deploy Trustline on Stellar, but to enable a full replication of permissioned DeFi workflows (as seen in DFI Labs on EVM) by integrating directly with core Stellar DeFi services.
Preliminary POC on Stellar/Soroban:
https://www.youtube.com/watch?v=ZJkyTd0-CXo
https://stellar-poc-escrow.trustline.id/
https://stellar-poc-registry.trustline.id/
https://github.com/TrustLine-id/stellar-poc-registry-escrow
Project's analytics
 ->
https://docs.google.com/document/d/18RCoOCcVhBeG_NYCV_4ONlqiRheTAUPsimbKPwwG6vs
Requested Budget
$133.6K
Traction Evidence
First Pilot Success:
Strategic collaboration with DFI-Labs, an AMF-regulated algorithmic hedge fund (€40M+ AUM). Deployed a Trustline-secured, Tier 1 insured vault and adapter on Hyperliquid:
https://vault-investor.trustline.id/?chainId=999&contractAddress=0x1E21A452a6F965bAF881Ff1e1b8a2E0724c57eAA
https://vault-manager.trustline.id/?chainId=999&contractAddress=0x1E21A452a6F965bAF881Ff1e1b8a2E0724c57eAA
We integrate seamlessly with established DeFi protocols (e.g., Morpho) without code changes, an approach we will replicate on Stellar.
LOI:
https://drive.google.com/file/d/1o_QoWNz7hKiSSgF0VURbab6fIP-8ld6e/view?usp=drive_link
Second pilot
 planned with a high-profile stablecoin.
Insurer Engagement:
 Secured support from top insurance brokers like Aon and Howden, building framework and capacity for the DFI-Labs pilot, which will be applicable to other on-chain services including on Stellar.
Market Demand:
DeFi: Asset allocators (Gauntlet -
https://www.gauntlet.xyz/
, Steakhouse -
https://www.steakhouse.financial/
) pending competitive insurance pricing.
TradFi: Active discussions with JPMorgan and ABN AMRO to embed smart contract services.
Partnership Ecosystem: Key partners, including Chainalysis (
https://www.chainalysis.com/
), Cyvers (
https://cyvers.ai/
), Nethermind (
https://www.nethermind.io/
), and DFNS (
https://dfns.co/
), endorse Trustline as best practice. Supported by XRPL Commons (
https://www.xrpl-commons.org/
) and Techstars (
https://www.techstars.com/
).
Stellar Traction:
LOI with
Cushion
 (SCF#42 -
https://www.cushion.trade/
):
https://drive.google.com/file/d/11FAsGBUmtneXtvoBI6EQgJ1oRUM12P-w/view?usp=sharing
Discussions with
Minah
 (SCF#38 -
https://minah.io/
),
Peridot
 (SCF#38 -
https://peridot.finance
),
Lobster
 (SCF#36 -
https://www.lobster-protocol.com/
)
All LOIs we can publicly share:
https://drive.google.com/drive/folders/1BdlS5Dqhye-8DAfLLFLEwPAEz-eVUKxZ?usp=sharing
Tranche 1 (Deliverable Roadmap) - MVP
[Deliverable 1 - Soroban ValidationEngine (core contract)]
Brief description: Implement the Soroban-native ValidationEngine contract responsible for recording and serving validation proofs (short-lived or TTL-based), emitting events, and exposing verification functions callable by service contracts.
How to measure completion:
Source code published in the project repository
Contract compiles and deploys to Stellar test environments
Public functions available to validate and register proofs, allow another contract to check the validity of a transaction, and consume the proof associated with a validated transaction.
Unit tests covering success/failure and expiry paths
Contract interface documented (README + function specs)
Estimated date of completion: Mid Month 1
Budget: $17,800 (engineering)
[Deliverable 2 - Backend Stellar publishing module (Soroban RPC)]
Brief description: Add a Stellar/Soroban publisher module to the Kotlin/Ktor backend that can publish validation proofs on-chain by invoking the ValidationEngine contract through Soroban RPC.
How to measure completion:
A pre-validation endpoint compatible with stellar is available on Trustline’s backend
A minimal harness that calls the pre-validation endpoint and triggers proof publication, so reviewers can reproduce the MVP flow
Documentation for RPC endpoints
Estimated date of completion: End of Month 1
Budget: $16,500 (engineering + infra)
[Deliverable 3 - Soroban contract integration SDK (Rust crate)]
Brief description: Create a reusable Rust crate to make Soroban contracts Trustline-compatible (including a Trustline Firewall contract).
How to measure completion:
Published crate source in the repository
Example service contract demonstrating one protected method using the SDK
Trustline Firewall contract included in the repository
Documentation: integration guide + code snippets
Test suite verifying intent hash determinism and verification calls
Estimated date of completion: Early Month 2
Budget: $10,200 (engineering)
Budget Tranche 1: $44,500
Tranche 2 (Deliverable Roadmap) - Testnet
[Deliverable 1 - TypeScript Web SDK (Stellar pre-validation)]
Brief description: Implement a Stellar-enabled Trustline WebSDK layer to build intent payloads and trigger backend pre-validation from Stellar frontends, including standardized error/status handling for validation outcomes. The SDK manages the validation UX (e.g., OTP/MFA steps) and returns the outcome to the dApp; the dApp/wallet remains responsible for building, simulating, signing, and submitting the user transaction.
How to measure completion:
A new package (or module) available in the repository
Documentation for the API
Example frontend flow that: (1) builds intent, (2) calls pre-validation, (3) receives a validation outcome, (4) exposes the result so the user can submit the final on-chain transaction
Estimated date of completion: End of Month 2
Budget: $24,400 (engineering)
[Deliverable 2 - End-to-end Testnet PoC with reference contracts]
Brief description: Deliver a working Testnet proof-of-concept demonstrating Trustline enforcement on a representative Soroban service contract, with the preferred reference implementation being a Trustline-protected SEP-56-compatible tokenized vault. The PoC will show how Trustline can gate standardized vault operations and selected privileged vault-management actions through pre-transaction validation and on-chain proof consumption. The vault will be designed with an adapter-ready architecture to demonstrate how future pluggable yield strategies could be supported.
How to measure completion:
Reference Soroban contract deployed to Stellar testnet
Public demo frontend (or reproducible local setup) showing allow/deny cases
Runbook describing deployment, configuration, and troubleshooting
Estimated date of completion: Mid Month 3
Budget: $19,300 (engineering + infra)
[Deliverable 3 - Stellar project onboarding support in Trustline backend/frontend]
Brief description: Implement/extend a backend onboarding API that enables Stellar/Soroban projects to register with Trustline and configure permissioning for their use case. This includes:
Storing/loading project owner and admin metadata.
Storing project configuration (e.g., Client associated information, Stellar network selection, policies, and service contract identifiers).
Returning the identifiers required by client apps (e.g., Project ID, Client ID, the ValidationEngine contract address/ID if relevant, etc).
How to measure completion:
Trustline’s Onboarding Website working to configure Stellar projects
Associated validation flow working as expected according to the configuration provided during onboarding
Estimated date of completion: Early Month 4
Budget: $23,100 (engineering)
Budget Tranche 2: $66,800
Tranche 3 (Deliverable Roadmap) - Mainnet
[Deliverable 1 - Mainnet deployment & operational readiness]
Brief description: Deploy the ValidationEngine contract to Stellar mainnet and enable mainnet publishing in the backend with production configuration, monitoring, and safeguards.
How to measure completion:
ValidationEngine contract deployed on mainnet and addresses published
Backend configured for mainnet Soroban RPC and able to publish proofs reliably
Estimated date of completion: Mid Month 4
Budget: $12,100 (engineering + infra)
[Deliverable 2 - Developer documentation & integration examples]
Brief description: Provide complete documentation for integrating Trustline into Soroban contracts and Stellar-enabled frontends, including example repositories.
How to measure completion:
Documentation published in repository (integration guide, API reference)
At least one end-to-end example project (contract + backend + frontend)
Clear instructions to reproduce on mainnet-like settings
Estimated date of completion: End of Month 4
Budget: $10,200 (engineering)
Budget Tranche 3: $22,300
Team
Team is fully dedicated to Trustline, which is a full-time project, and as already a strong experience with smart contract development and protocol integration.
Team: led by two experienced Techstars Founders ('24) with deep expertise in traditional finance and blockchain security.
Ben Simatos, CEO, Techstars '24
London, ex-Goldman Sachs/JPMorgan
has a worked 15 years in capital markets and traditional finance
10+ years entrepreneur experience
started blockchain companies since 2018 (Founder + CFO/Chief Economist/COO for IOV/Starname 2018-21 on Cosmos + ICO (
starname coin market cap
,
medium
),
Founder + CPO Legendary Plays in 2022-24 on Tezos/Polygon (
https://www.instagram.com/legendaryplays/officiel/?hl=en
),
Techstars backed Founder + CEO Securely 2022-24 on EVM chains (
linkedin post
))
He focuses on institutional demand, insurance strategy, partnerships, and ecosystem development.
Linkedin
JC Rona, CTO, Techstars '24
Paris, ex-Archos (
https://www.archos.com/
), has 20 years in technology and product development,
expert in security, software and hardware development.
build SW/HW blockchain products since 2017 (including 2 HW Wallet: Safe-T mini, Safe-T touch) (
article
)
(
github
,
project featured
,
another project featured
)
He focuses on tech architecture, infrastructure, operational security, and development, including:
EVM/Solana/Stellar contracts (Solidity, Rust)
Frontend (React JS/TS, Next.js, Vite, Angular)
Backend (Kotlin, Java, Node.js)
Linkedin
Alexandre Manuel, Senior Dev
Smart contract developer, Solidity teacher at EPITA (top French computer science school)
Built mining services in 2017
He focuses on contract development, and frontend development, with extensive experience in:
Solidity
Rust
Kotlin
JavaScript
TypeScript
Linkedin
Jean-Christophe Rona
```
