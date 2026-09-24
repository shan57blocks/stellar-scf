# VERSO — Peru's First Regulated Stellar Anchor: PEN/USD On/Off-Ramp: SDF Anchor Platform

Source: https://communityfund.stellar.org/submissions/rec4pEX17tlzuaaex (SCF #44, awarded $110.0K, category: Financial Protocols)

- Website: https://www.versotek.io/
- Architecture doc: https://github.com/WildorAP/verso-tad/blob/main/technical-architecture-document.md

## Links in the submission

- https://github.com/WildorAP/verso-tad/blob/main/technical-architecture-document.md
- https://drive.google.com/drive/folders/1xMgAN0pElm_O9-q7UpSMlqdvelq7kr0X?usp=sharing

## Submission text (as published)

```text
Products & Services
VERSO will implement the first regulated PEN/USD to USDC conversion platform in Peru, using the SDF Anchor Platform as an integration layer with Stellar.
1. SEP-24 Conversion Platform (PEN/USD to USDC)
With Stellar: The Anchor Platform manages all SEP protocol interactions; USDC is settled on Stellar. Users select VERSO (Lobstr, Freighter, Vibrant, etc.) to deposit PEN or USD and receive USDC, or send USDC and receive PEN or USD through the Peruvian CCI/CCE banking network.
Impact: Activates a new regulated Anchor that has not yet been used in Latin America.
2. Wallet Authentication + KYC Integration SEP-10
With Stellar: The Stellar key pair signs the SDF challenge to the VERSO backend, where the signature is verified and the wallet is linked to VERSO's existing compliance records (KYC, OpenSanctions, Travel Rule).
Impact: New users complete the onboarding process through the interactive web interface SEP-24. The Stellar wallet becomes a verified and compliant identity.
3. SEP-38 Real-Time PEN/USD to USDC Quotes
Using Stellar: The Anchor Platform calls VERSO's /callbacks/rate before each transaction; the rate, fee, and final amount are displayed in Stellar wallets before confirmation.
Impact: Full price transparency thanks to VERSO's real-time pricing engine.
4. SEP-1 Anchor Discovery
Using Stellar: The stellar.toml file published allows any Stellar wallet, application, or aggregator to discover VERSO without manual configuration.
Impact: One-time implementation. VERSO is automatically visible to the entire Stellar ecosystem.
5. Semi-Automatic Fiat Settlement
Using Stellar: Automated transaction detection alerts the VERSO system when a payment is confirmed. The operator then reviews and manually approves it, and the Anchor Platform updates the transaction status. Finally, the USDC is released on Stellar.
Impact: Eliminates manual bank polling. The operator receives instant notification instead of periodic checks. Settlement time reduced from hours to under 30 minutes, while maintaining full manual approval for each transaction.
Requested Budget
$110.0K
Traction Evidence
VERSO is not a concept pitch. We are a profitable, regulated stablecoin exchange processing real client transactions in Peru since September 2022 — and we are building the first PEN/USDC Anchor on Stellar by an officially licensed VASP.
━━━ BUSINESS TRACTION ━━━
We have processed ~$24M USD in gross exchange volume since founding, declared to Peru's tax authority SUNAT (RUC 20609951088) — full accounting records included in our supporting documentation. In the first four months of 2026 alone, our volume reached ~$4.9M USD — +144% growth versus the same period in 2025 (~$2.0M).
Our 2025 full-year volume was $8.8M USD, +45% from 2024's $6.1M USD. At our current monthly pace we are tracking to ~$14.6M USD for full-year 2026 — +66% over 2025. April 2026 was our record month at $1.4M USD.
Since launch we have served 100+ active clients per month across ~700 monthly transactions, with 15,000+ cumulative operations processed.
VERSO is fully bootstrapped, profitable since our first year, with zero SUNAT debt and no outstanding regulatory resolutions.
━━━ REGULATORY STANDING ━━━
VERSO holds a formal VASP license under Resolution SBS N° 02648-2024 — among Peru's first — issued by the Superintendencia de Banca, Seguros y AFP (SBS). This is the license required by Peruvian law to operate a stablecoin exchange.
Our compliance infrastructure is operational and battle-tested in production:
SPLAFT (AML/CFT program) live since 2022 — Compliance Officer formally registered on UIF-SBS SISDEL platform (Arts. 5-7, Res. SBS 02648-2024)
Travel Rule implemented (Art. 24), embedded in every transaction
PEP and global sanctions screening via OpenSanctions on every client onboarding
Beneficial ownership tracking and IP geolocation fraud detection
Annual KYC refresh — every active client undergoes periodic due diligence renewal and information update, as required under SPLAFT protocols
VERSO has completed a formal SBS supervisory examination of our AML/CFT processes — our compliance infrastructure has been independently verified by Peru's banking regulator, not just licensed by it.
Our fiat rails cover every bank in Peru via CCI/CCE — Peru's national interbank settlement network — with active accounts at BCP and Interbank in both PEN and USD. Both banks conduct their own annual compliance verifications of VERSO as a regulated financial counterpart — verifications VERSO passes each year.
━━━ TECHNICAL READINESS ━━━
Before this grant, we built three production systems in-house:
versotek.io
 — client portal with automated KYC/AML (DIDIT), multi-network wallet, and full transaction management.
https://www.versotek.io
VERSO Compliance System — our own SPLAFT/AML engine: PEP and sanctions screening (OpenSanctions), Travel Rule, automated accounting, beneficial ownership, and IP fraud detection.
SUNAT API — a FastAPI microservice indexing Peru's complete taxpayer registry (millions of records), sub-millisecond fuzzy search via pg_trgm, zero external API dependency.
━━━ EXTERNAL VALIDATION ━━━
In May 2026, VERSO was selected for the UTEC Ventures Pre-Acceleration Program — one of Peru's most competitive university startup programs, independently evaluated by UTEC, Peru's leading technology university.
Supporting documentation (SBS Resolution N° 02648-2024, SUNAT declarations 2022–2026, UTEC Ventures acceptance letter):
https://drive.google.com/drive/folders/1xMgAN0pElm_O9-q7UpSMlqdvelq7kr0X?usp=sharing
Tranche 1 (Deliverable Roadmap) - MVP
Goal: VERSO Anchor discoverable on the Stellar ecosystem and authenticated on testnet. Any SEP-compatible Stellar wallet can discover VERSO via stellar.toml, a registered client can authenticate with their Stellar wallet, and a first simulated deposit is verifiable on Stellar Expert.
Deliverable 1: SEP-1: Anchor Platform live on testnet + stellar.toml published ($9,000)
Description: SDF Anchor Platform (Docker) deployed on Railway in testnet configuration, wired to VERSO's backend via business callback interface. Hot wallet created on Stellar testnet with USDC trustline active. AWS KMS-backed key management configured for Stellar transaction signing (no plaintext private keys). CI/CD pipeline live (GitHub Actions to Railway). stellar.toml published declaring VERSO currencies, services, and SEP endpoints. Public integration guide for stellar.toml discovery published in GitHub repository.
How to measure completion:
stellar.toml published with zero validation errors in Stellar Laboratory.
VERSO Anchor discoverable in at least one publicly available SEP-compatible Stellar wallet on testnet.
Anchor Platform health endpoint live and returning 200.
Hot wallet address published in public GitHub README, visible on Stellar Expert testnet.
______________________________________________________________________________________________________________________________________________
Deliverable 2: SEP-10: Wallet authentication connected to VERSO's compliance system ($8,000)
Description: VERSO SEP-10 callback operational on testnet. The Stellar wallet signs the SDF challenge; VERSO's backend verifies the signature, checks KYC status in VERSO's compliance system, and issues JWT for existing VERSO clients. New users redirected to DIDIT onboarding flow. Full authentication loop confirmed with a Stellar testnet wallet. SEP-10 endpoint documented with code examples in the public GitHub repository.
How to measure completion:
SEP-10 authentication flow fully operational on testnet.
Existing VERSO client authentication confirmed: KYC status lookup operational in VERSO's compliance system.
New wallet onboarding redirect confirmed.
______________________________________________________________________________________________________________________________________________
Deliverable 3: First end-to-end simulated deposit on testnet ($5,000)
Description: Backend deposit callback cycle fully operational on testnet. No SEP-24 interactive webview yet (delivered in T2). A deposit is initiated directly through the Anchor Platform; the Anchor Platform calls VERSO's backend deposit callback; VERSO backend processes the request, checks KYC status, and places the transaction in pending state awaiting fiat confirmation; the VERSO operator manually confirms the simulated PEN receipt; VERSO backend notifies the Anchor Platform; USDC is disbursed to the user's Stellar wallet on testnet.
How to measure completion:
Full deposit callback cycle operational: Anchor Platform calls VERSO backend, KYC check passes, transaction reaches pending state, operator confirms, USDC disbursed on testnet
All transaction states tracked from initiation through USDC disbursement
On-chain USDC disbursement confirmed on Stellar testnet
Tranche 2 (Deliverable Roadmap) - Testnet
Goal: Full SEP suite operational on testnet: interactive on/off-ramp via SEP-24, real-time quotes via SEP-38, Horizon reconciliation live. Existing VERSO clients successfully execute end-to-end testnet flows. Mainnet switch ready behind feature flag.
Deliverable 1: SEP-24: Interactive on-ramp and off-ramp on testnet ($16,000)
Description: Full deposit (PEN to USDC) and withdrawal (USDC to PEN) flows operational via Anchor Platform + VERSO backend callbacks on testnet. SEP-24 interactive webview (React 18 + Vite + TypeScript) branded as VERSO, selectable from any SEP-24 compatible Stellar wallet on testnet. Phase 1 manual settlement: VERSO operations team confirms simulated CCI/CCE receipt and triggers USDC disbursement.
How to measure completion:
On-ramp flow operational on testnet: wallet authentication, CCI/CCE deposit instructions, and USDC disbursement all complete end-to-end
Off-ramp flow operational on testnet: USDC withdrawal received by VERSO, fiat disbursement confirmed
10+ testnet on-ramp and off-ramp transactions completed on Stellar testnet
______________________________________________________________________________________________________________________________________________
Deliverable 2: SEP-38: Real-time PEN/USDC quotes on testnet ($9,000)
Description: Real-time exchange rate quotes via Anchor Platform SEP-38 endpoint, calling VERSO's /callbacks/rate endpoint on testnet. Each quote includes: live PEN/USDC rate from VERSO's pricing engine, VERSO fee, final USDC amount, 30-second expiration window. Quotes displayed inside at least one SEP-38 compatible Stellar wallet on testnet before any transaction confirmation.
How to measure completion:
SEP-38 endpoint operational on testnet: valid quotes returned including live rate, fee, and final USDC amount
Quotes displayed inside at least one SEP-38 compatible Stellar wallet before transaction confirmation
10 consecutive quotes validated against VERSO's live pricing engine with matching results
______________________________________________________________________________________________________________________________________________
Deliverable 3: Horizon streaming reconciliation: 14-day no-unresolved-discrepancies report on testnet ($8,000)
Description: Horizon Streaming API service listening in real time to every USDC movement on VERSO's testnet hot wallet. Each event recorded in PostgreSQL and reconciled with on-chain balance. Any discrepancy fires a CloudWatch alert. Schema: stellar_tx_hash, amount_usdc, amount_pen, amount_usd, direction, kyc_status, fiat_rail, fiat_status, anchor_callback_id, created_at, updated_at.
How to measure completion:
Horizon streaming service operational: every USDC movement on the testnet hot wallet captured in real time and reconciled with internal ledger
14-day monitoring period completed with no unresolved discrepancies between on-chain balances and internal ledger records
CloudWatch alerts configured and tested: any discrepancy triggers an alert
Tranche 3 (Deliverable Roadmap) - Mainnet
Goal: VERSO Anchor live on mainnet, operating SEP-1/10/24/38 compliant infrastructure on Stellar with real PEN/USDC transactions on-chain. CCI/CCE Phase 2 semi-automated settlement operational. VERSO submitted for listing in the Stellar Anchor Directory. Codebase prepared for external security review. Available for ecosystem user testing upon mainnet launch.
Deliverable 1: Mainnet launch: SEP-1/10/24/38 all live on Stellar mainnet ($15,000)
Description: All mainnet configurations (Anchor Platform endpoints, mainnet wallet addresses, mainnet USDC issuer GA5ZSEJYB37JRC5AVCIA5MOP4RHTM335X2KGX3IHOJAPP5RE34K4KZVN) committed to codebase and verified before the feature flag is switched. Feature flag switched to mainnet. All four SEPs operational on Stellar mainnet: SEP-1 (stellar.toml updated for mainnet), SEP-10 (wallet auth against mainnet accounts), SEP-24 (interactive on/off-ramp with real PEN/USDC flows), SEP-38 (live quotes inside Stellar wallets). Hot wallet on mainnet with AWS KMS signing. Cold wallet dual-approval protocol active.
How to measure completion:
VERSO hot wallet active on Stellar mainnet with USDC trustline
stellar.toml updated for mainnet with zero validation errors in Stellar Laboratory
SEP-10 authentication flow operational on mainnet
SEP-38 live quotes displayed inside a Stellar wallet before each real transaction
Deliverable 2: CCI/CCE Phase 2: semi-automated fiat settlement operational ($12,000)
Description: Automated transaction detection integrated through available banking integrations and operational monitoring tools: confirmed PEN/USD receipt triggers an instant alert to the VERSO operator via VERSO's backend. The operator reviews the transaction, applies AML/compliance checks, and manually approves disbursement, at which point the Anchor Platform updates the transaction status and releases USDC to the user's Stellar wallet. Significantly reduces manual monitoring and reconciliation effort while maintaining full operator approval for each transaction, as required by VERSO's VASP obligations under SBS/UIF. Target average settlement time reduced from hours to under 30 minutes. Covers BCP and Interbank (Banco Internacional del Perú) via CCI/CCE, providing nationwide banking reach through the CCI/CCE network.
How to measure completion:
Average settlement time of approximately 30 minutes or less demonstrated across 30 consecutive completed transactions
Transfers from at least 3 different Peruvian banks (BCP, BBVA, Scotiabank) processed successfully
Operational runbook documenting the operator review and approval workflow published in public GitHub repository
Deliverable 3: Anchor operational proof: on-chain transactions verified on Stellar Expert + VERSO submitted to Anchor Directory ($3,000)
Description: Real PEN/USDC transactions processed through VERSO's mainnet Anchor, fully verifiable on Stellar Expert without trusting VERSO's claims. On-ramp (PEN to USDC) and off-ramp (USDC to PEN) both demonstrated with real transactions. VERSO submitted for inclusion in the Stellar Anchor Directory, with all published listing requirements completed, enabling discoverability by any developer or wallet integrating with the Stellar ecosystem.
How to measure completion:
10+ real mainnet on-ramp and off-ramp transactions completed, each verifiable on Stellar Expert
VERSO submitted for inclusion in the Stellar Anchor Directory with all published listing requirements completed
Mainnet account history publicly viewable on Stellar Expert showing on-chain USDC flows
Deliverable 4: Pre-launch security hardening + audit preparation ($8,000)
Description: Internal code review and security hardening of the Anchor integration before mainnet launch: edge-case testing, input validation across all SEP callback endpoints, AWS KMS key management verification, SEP-10 challenge/response security review, and webhook authentication hardening. Codebase prepared for external security review according to SDF recommendations.
How to measure completion:
All SEP callback endpoints reviewed for security: input validation confirmed, KMS signing verified
Edge-case test suite covering invalid SEP-10 signatures, malformed callback payloads, duplicate transaction IDs, and webhook replay attacks — all passing on CI
Internal hardening review completed: all identified issues resolved before mainnet launch
Deliverable 5: Public technical documentation ($6,000)
Description: Comprehensive public documentation published on GitHub covering the complete VERSO Anchor implementation: SEP implementation guide (SEP-1/10/24/38 as deployed), mainnet architecture diagram showing Anchor Platform, VERSO backend callbacks, and CCI/CCE flow, callback endpoint reference with request/response examples, and operational runbook documenting VERSO's operator review and approval workflow. Enables SDF to audit the implementation independently and any Stellar wallet team to evaluate VERSO as a regulated PEN/USDC Anchor.
How to measure completion:
SEP implementation guide covers SEP-1/10/24/38 as deployed
Mainnet architecture diagram published and consistent with the Technical Architecture Document
Callback endpoint reference and operational runbook complete in public GitHub repository
Team
1. Wildor Apaza — Founder, CEO & Lead Developer
Engineering degree from Universidad Nacional de San Antonio Abad del Cusco
(UNSAAC). Master's in Finance at Universidad del Pacífico (2025–2027, in progress), ranked #1 in Peru and #1 in Latin America by Eduniversal Best
Masters. Completed Full-Stack Development at UTEC Posgrado (2025) and React + Vite specialization at
TECSUP Peru (2025). Former intern at the Inter-American Development Bank (IDB). Founded VERSO in September 2022 and personally designed and built the company's three production systems:
*
versotek.io
 — customer portal (Django, DIDIT KYC, multi-network wallet risk verification, AWS S3)
* BASE_DE_CLIENTES — internal compliance, operations, treasury, multi-currency accounting, and fraud monitoring platform
* SUNAT_API — FastAPI microservice integrating Peru's taxpayer registry with millions of records and fuzzy-search capabilities
Technical stack includes Django, FastAPI, Python, React, Vite, TypeScript, AWS, and blockchain integrations across TRON, Ethereum, Solana, and BNBChain.
For this project, Wildor will lead the deployment of the SDF Anchor Platform, implementation of SEP integrations, Django business callback
APIs, and the Anchor's React/Vite user interface.
LinkedIn:
https://www.linkedin.com/in/wildor-apaza-627465109
GitHub:
https://github.com/WildorAP
2. Juan Pablo Cruz Ramos — Operations & Treasury Manager Engineering degree from Universidad Nacional de San Antonio Abad del Cusco (UNSAAC). Full-time with exclusive dedication to VERSO. Juan Pablo oversees daily exchange operations, treasury management, liquidity coordination, fiat settlement workflows, and execution across VERSO's PEN, USD, and stablecoin operations. He manages settlements through Peru's CCI/CCE banking infrastructure, coordinating transfers to and from all major Peruvian banks. His role is central to the operational execution of a business that processed approximately $8.8M USD in exchange volume during 2025. For the Stellar Anchor, Juan Pablo will oversee deposit and withdrawal operations, liquidity management, treasury reconciliation, and the transition from manual fiat settlement to semi-automated settlement workflows.
3. Gabriela Carrión — Compliance Officer
Economics degree from Pontificia Universidad Católica del Perú (PUCP). Full-time with exclusive dedication to VERSO. Gabriela is formally registered as Compliance Officer with Peru's UIF-SBS through the SISDEL platform, as required under SBS Resolution No. 02648-2024. She leads VERSO's AML/CFT compliance program, including customer due diligence, KYC verification, beneficial ownership analysis, sanctions and PEP screening, suspicious activity monitoring, regulatory reporting, and compliance with UIF-SBS obligations.
For the Stellar Anchor, Gabriela will oversee all compliance controls associated with on-chain and fiat transactions, ensuring adherence to AML requirements, Travel Rule obligations, and VASP regulatory standards.
Together, the team combines software engineering, treasury operations,
regulated financial services, and AML/CFT compliance expertise — the core
capabilities required to deploy and operate Peru's first regulated Stellar
Anchor.
gabriela carrion
Juan Pablo Cruz
WILDOR APAZA CHINO
```
