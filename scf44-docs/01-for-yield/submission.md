# For Yield: EU MiCA Capital Layer for Soroban DeFi

Source: https://communityfund.stellar.org/submissions/recDqMIZnhmuWnq2f (SCF #44, awarded $144.0K, category: Financial Protocols)

- Website: https://www.for-yield.com
- Architecture doc: https://drive.google.com/file/d/1iepVOIiSqV4DFtMhH8AxCQgJWOuQsTnP/view

## Links in the submission

- https://drive.google.com/file/d/1iepVOIiSqV4DFtMhH8AxCQgJWOuQsTnP/view
- https://github.com/Foryield/soroban-yield-vault
- https://drive.google.com/file/d/1um_iE789ZrMfnSC4bhg2vYY0qoh8gBq6/view?usp=sharing

## Submission text (as published)

```text
Products & Services
The grant funds the on-chain integration layer of For Yield, scheduled to ship alongside our MiCA PSCA agrement in October 2026. Our entire Integration Track scope is sourced from the official SCF Integration List.
1. Soroban YieldVault Smart Contract. Core vault accepting USDC and EURC (EURC integrated via StellarAssetContract wrapper). Capital allocated across Blend v2, Aquarius LP, and Soroswap routing via DeFindex allocator logic. Open-sourced under MIT. Stellar used: Soroban contract execution, native USDC settlement, EURC SAC wrapper, DeFindex routing. Impact: designed to become the first regulated EU access layer to Soroban DeFi once the PSCA agrement is granted (target October 2026).
2. Soroban Performance Fee Module. Atomic on-chain split of performance fees and management fees in EURC, under high-water mark logic. Open-sourced. Stellar used: Soroban contract, EURC distribution, Atomic Splits. Impact: institutional-grade fee accounting on-chain.
3. Wallet Onboarding Stack. Stellar Wallets Kit (already live in testnet vault UI with Freighter) extended to multi-wallet support, plus DFNS embedded wallets via email and Google login as the primary onboarding path for non-crypto-native HNW clients. Stellar used: SCF Integration List components (DFNS, Stellar Wallets Kit). Impact: zero-friction onboarding for HNW investors who do not hold seed phrases.
4. DEX Routing. Soroswap aggregator routing across Phoenix and SDEX, with Aquarius (~$40M TVL) as a fallback liquidity venue. Stellar used: Soroswap, Aquarius, SDEX. Impact: best-execution swap layer for vault rebalancing.
5. Cross-Chain Onboarding. Allbridge for EVM and Solana stablecoin inflows to Stellar. Primary onboarding path remains EURC via fiat ramp (regulated channel). Stellar used: Allbridge (SCF Integration List). Impact: crypto-native power users from other ecosystems can enter For Yield without bridging manually.
6. Compliance Audit Trail. Soroban Events for AMF-compliant audit trails: every deposit, redemption, fee accrual and reallocation event emits a structured event consumed by our compliance dashboard. Stellar used: Soroban Events. Impact: automated regulator-grade reporting.
Supporting institutional infrastructure (NOT Integration Track scope, NOT funded by this grant): DFNS for primary custody and embedded wallets (on SCF Integration List), Fireblocks MPC as institutional custody backup option, Elliptic or Notabene (Travel Rule + KYT), Certora Soroban smart contract audit (covered by Stellar LaunchKit audit credit at T2 review, not by this grant).
Requested Budget: $144,000 USD Composition: 1,800 engineering hours at a blended team rate of $80/hr, distributed across 3 equivalent tranches of $48,000 (600h each). Per-deliverable hours and roles documented in the Budget Methodology block above and in each deliverable below. Development work only. Certora audit covered by Stellar LaunchKit credit at T2 review, not by grant.
BUDGET METHODOLOGY
Total: $144,000 USD = 1,800 engineering hours at a blended team rate of $80 per hour. The blended rate averages a senior Soroban/backend lead, a frontend engineer, a DeFi integration engineer, and shared QA/security-prep. Development work only (no marketing, no external audit, no legal). The Soroban smart contract audit (Certora) is NOT funded by this grant; it is covered by the Stellar LaunchKit audit credit unlocked at T2 review. 3 tranches of $48,000 USD = 600 hours each.
ROLES
- P. Barnes (CTO), Lead Soroban / backend engineer
- M. Ortiz, Frontend engineer
- A. Bensalama, DeFi integration engineer
- Shared, QA and security-prep
ROLE TOTALS (1,800 h, $144,000)
- Barnes (CTO/Soroban): 1,020 h = $81,600
- Ortiz (frontend): 315 h = $25,200
- Bensalama (DeFi): 275 h = $22,000
- QA/security-prep: 190 h = $15,200
- TOTAL: 1,800 h = $144,000
Requested Budget
$144.0K
Traction Evidence
Live pilot validation (the strongest traction signal):
- 50 HNW clients participating in the pre-agrement market test prior to PSCA filing.
- Approximately $7M USD aggregate AUM, ~$7.14M snapshot June 2026, verified via masked DeBank screenshots (uploaded as ForYield On-chain AUM Evidence.pdf) across 5 curator wallets: 4 Gnosis Safe multisigs plus 1 longer-standing single-signature curator wallet holding the Morpho position predating the multisig restructuring. Positions held on Lagoon, f(x) Protocol, Ethena, Fluid, Lighter, USDC, apxUSD, Morpho. Figures are a live snapshot and move with market prices.
- Demand is proven; the PSCA agrement scales a validated pre-agrement market test to regulated retail-accredited distribution.
DeBank evidence (June 2026 snapshot, masked):
Uploaded as ForYield On-chain AUM Evidence.pdf.
On-chain evidence of the aggregate AUM via DeBank across five curator wallets on Ethereum mainnet. Figures are a live snapshot and move with market prices.
- Curator wallet 1: $2,399,007 USD (Lagoon, f(x) Protocol, Ethena) [Gnosis Safe multisig]
- Curator wallet 2: $497,480 USD (Lagoon, Fluid) [Gnosis Safe multisig]
- Curator wallet 3: $614,943 USD (Lagoon, Lighter, USDC) [Gnosis Safe multisig]
- Curator wallet 4: $1,117,282 USD (Morpho) [Single-signature curator wallet, longer-standing position predating the multisig restructuring]
- Curator wallet 5: $2,508,329 USD (apxUSD, USDC) [Gnosis Safe multisig]
- Aggregate: ~$7.14M USD (June 2026 snapshot)
All wallet addresses masked in the uploaded screenshots. DeBank platform UI, position breakdown, and snapshot date are visible. Wallet 3 +100% change indicator (a DeBank artifact for recently-created wallets with no price baseline) is cropped from the screenshot before upload.
Screen shots :
https://drive.google.com/file/d/1um_iE789ZrMfnSC4bhg2vYY0qoh8gBq6/view?usp=sharing
Regulatory milestones (the strongest moat in our dossier):
- PSCA agrement MiCA dossier (25 sections + DORA) filed with AMF in April 2026, agrement targeted October 2026.
- EP simplifie license filed in parallel with ACPR (article L.522-11-1 CMF). This dual-licence setup is unique in France.
- Cybersecurity audit by ACCEIS passed in March 2026.
Distribution partners (Day-1 distribution stack):
- Saturnin Paulet (For Yield Director of Strategy, 25 percent shareholder, Lausanne CH) runs Crypto and Macro, a leading Swiss-based crypto and macro newsletter with 40,730 active newsletter subscribers (verified June 2026, Salesforce CRM), featured on BFM Business.
- Distribution partnerships signed with two regulated EU intermediaries: a BaFin-licensed MiFID II STO platform passporting across 27 EU member states, and a DORA-compliant ISO 27001:2022 certified institutional tokenization platform. Specific partner names available under NDA on reviewer request.
Sales channel: French CGPI (independent wealth advisor) network building since January 2026 under Cosmo Di Giuseppantonio (Head of Sales, ex-Uptoo). First Letters of Intent signed May 2026.
Founder track record:
- Quentin Hopp (Founder & President): For Mining LTD ($10M+ revenue, 10,150+ machines, 500+ B2B clients 2024-2025). Previously founder OMICRON 21 ($1.2M revenue), ex-AXA Wealth Services.
- Julien Daubert (Managing Director): HEC Paris Executive, founder/CEO 10H11 (data science for regulated fintechs, 14 years).
- Alexandre Pellenq (CRCO): PSCA compliance lead.
- Pierrick Barnes (CTO/CISO): Soroban, DFNS (primary custody, on SCF Integration List), Fireblocks MPC (institutional backup), Rails 8.1, Next.js 16.
AMF-validated business plan: Year 1 25M EUR AUM, 825K EUR revenue, 270K EUR net. Year 3 100M EUR AUM, 3.3M EUR revenue, 1.03M EUR net. Profitable Year 1 in every stress scenario.
Tranche 1 (Deliverable Roadmap) - MVP
Deliverable 1: Soroban YieldVault Smart Contract (MVP).
Description: Implementation of the core vault contract. Accepts USDC deposits, mints proportional vault shares, supports a single initial allocation target (Blend v2 USDC pool). Includes admin functions, deposit and withdraw flows, and unit test suite covering the core mathematical invariants.
Measure: contract deployed to Stellar testnet with verifiable address, full test suite passing (200+ tests), unit-tested coverage above 90 percent.
Reviewer evidence: testnet contract ID, GitHub repo URL with merged PRs, deposit and withdraw transaction hashes on testnet.
Estimated completion: T+5 weeks.
Hours and roles: 250 h total = Barnes 190 h + QA/security-prep 60 h.
Budget: $20,000 USD.
Deliverable 2: Wallet Onboarding Stack (DFNS embedded + SWK production extension).
Note on SWK scope: Stellar Wallets Kit is already integrated and live in our testnet vault UI (
vault.for-yield.com
) with Freighter, work completed in advance of this grant. Deliverable 2 reallocated, total unchanged at $14,000:
- SWK production hardening: $2,800 (35 h Ortiz). Remaining work only: multi-wallet support beyond Freighter (xBull, Albedo, Lobstr, Ledger via the kit), mainnet network configuration, and robust signing/session/error handling.
- DFNS embedded-wallet onboarding: $11,200 (140 h: Barnes 90 h + Ortiz 50 h). The substantive remaining onboarding work: provisioning a Soroban-compatible embedded wallet from an email/social login so non-crypto-native users onboard with no extension and no seed phrase. DFNS is on the SCF Integration List.
Measure: SWK signing live on mainnet config across the listed wallets, and a DFNS-provisioned wallet completing a deposit on testnet.
Reviewer evidence: DFNS onboarding walkthrough video, multi-wallet connection screenshots, GitHub PRs.
Estimated completion: T+7 weeks.
Hours and roles: 175 h total (DFNS 140 h + SWK 35 h).
Budget: $14,000 USD.
Deliverable 3: EURC SAC Wrapper Integration.
Description: EURC is a Classic Stellar asset, not Soroban-native. This deliverable wraps EURC via the StellarAssetContract (SAC) so the YieldVault contract can accept EURC deposits and emit EURC redemptions.
Measure: EURC deposit and redemption transactions on testnet, with the SAC wrapper invoked.
Reviewer evidence: testnet transaction hashes, contract ID, walkthrough video.
Estimated completion: T+8 weeks.
Hours and roles: 175 h total = Barnes 120 h + QA/security-prep 55 h.
Budget: $14,000 USD.
Total Budget : 48,000 USD
Tranche 2 (Deliverable Roadmap) - Testnet
Deliverable 4: DEX Routing (Soroswap + Aquarius).
Description: Implementation of the vault's swap routing layer. Soroswap aggregator used as primary swap venue for rebalancing, with Aquarius LP pools (~$40M TVL) as fallback liquidity. Includes slippage protection, swap-fee accounting and best-execution selection logic.
Measure: vault successfully rebalances between USDC and EURC via Soroswap aggregator on testnet.
Reviewer evidence: testnet swap transaction hashes, walkthrough video, GitHub PR.
Estimated completion: T+10 weeks.
Hours and roles: 175 h total = Barnes 110 h + Bensalama 65 h.
Budget: $14,000 USD.
Deliverable 5: Multi-Protocol Allocator (DeFindex Routing).
Description: Integration of DeFindex as the allocator routing layer. The vault now allocates dynamically across Blend v2, Aquarius LP pools and Soroswap-routed positions based on yield, risk and capacity. Includes rebalance trigger logic and gas-optimized batch allocation.
Measure: vault performs multi-protocol allocation across at least 2 strategies via DeFindex on testnet.
Reviewer evidence: testnet contract address showing allocation distribution, transaction hashes for each allocation, walkthrough video.
Estimated completion: T+12 weeks.
Hours and roles: 200 h total = Bensalama 120 h + Barnes 80 h.
Budget: $16,000 USD.
Deliverable 6: Performance Fee Module + Compliance Audit Trail.
Description: Two interlocked components. (a) Soroban Performance Fee Module with high-water mark logic, atomic on-chain split of performance fees and management fees in EURC. (b) Soroban Events schema for AMF-compliant audit trails.
Measure: fee accrual and distribution executed on testnet; structured Soroban Events visible on Stellar Expert and consumed by For Yield's compliance dashboard.
Reviewer evidence: testnet contract addresses, event-emission transaction hashes, dashboard screenshot.
Estimated completion: T+16 weeks.
Hours and roles: 225 h total = Barnes 150 h + QA/security-prep 75 h.
Budget: $18,000 USD.
Tranche 2 unlocks Stellar LaunchKit audit credit (Certora, not funded by this grant) and infrastructure credit access.
Total Budget : 48,000 USD
Tranche 3 (Deliverable Roadmap) - Mainnet
Deliverable 7: Mainnet Deployment + Production Monitoring.
Description: All Soroban contracts deployed to Stellar mainnet, post-Certora audit. Production-grade monitoring dashboard live (uptime, contract health, allocation distribution, fee accrual tracking).
Measure: all contracts live on Stellar mainnet with verifiable addresses, post-audit, monitoring dashboard accessible to reviewers (read-only link).
Reviewer evidence: mainnet contract IDs, audit report summary, dashboard URL.
Estimated completion: T+20 weeks.
Hours and roles: 175 h total = Barnes 115 h + Ortiz 60 h.
Budget: $14,000 USD.
Deliverable 8a: Allbridge Cross-Chain Integration.
Description: integrate Allbridge Core so users holding assets on EVM or Solana can deposit into the vault without manually bridging first.
Measure: a testnet cross-chain deposit credited as vault shares, verifiable on Stellar Expert.
Reviewer evidence: bridge transaction hash on origin chain, Stellar inbound hash, mainnet vault deposit hash, walkthrough video.
Estimated completion: T+21 weeks.
Hours and roles: 75 h total = Barnes 75 h.
Budget: $6,000 USD (scoped per SCF Integration List estimate).
Deliverable 8b: Investor Dashboard.
Description: portfolio and reporting UI (positions, share price, performance, history). Includes login, position-masking for privacy, PDF export for AMF-compliant reporting.
Measure: dashboard rendering live on-chain vault state for a connected wallet.
Reviewer evidence: dashboard URL (read-only link), screenshots with masked positions.
Estimated completion: T+22 weeks.
Hours and roles: 100 h total = Ortiz 100 h.
Budget: $8,000 USD.
Combined D8a + D8b total: $14,000 (unchanged from original D8).
Deliverable 9: First Production Deposits + Full Reporting Cycle.
Description: First production deposits onboarded after the October 2026 PSCA agrement. Targeted minimum $1M USD AUM in the mainnet vault, with one full quarterly reporting cycle completed.
Measure: production AUM of at least $1M USD deployed across the live mainnet vault, one full reporting cycle completed with structured event log generated.
Reviewer evidence: mainnet vault address with TVL visible, NAV report PDF (masked counterparties), Stellar Expert link.
Estimated completion: T+24 weeks.
Hours and roles: 250 h total = Barnes 90 h + Ortiz 70 h + Bensalama 90 h.
Budget: $20,000 USD.
Total Budget : 48,000 USD
CONTINGENCY: AMF PSCA agrement delayed beyond October 2026.
The grant build is decoupled from the agrement. Tranches 1 and 2 (all testnet: MVP vault, EURC SAC, DEX routing, allocator, fee module, compliance event schema) do NOT depend on the agrement and ship on schedule regardless.
For Tranche 3, two deliverables touch production (D7 mainnet deployment, D9 first production deposits). If the agrement slips, the grant milestones remain achievable:
1. Mainnet deployment (D7) is infrastructure and does not require the agrement. Audited contracts deploy to mainnet on schedule.
2. D9 is measured by VERIFIABLE MAINNET DEPOSITS, not by onboarding new regulated clients. If the agrement is delayed, the first production deposits are made with the team's own capital under the existing pre-agrement market-test framework.
3. The three open-source Soroban primitives (vault, fee module, EURC SAC wrapper, MIT) are delivered regardless of the agrement.
Team
ForYield SAS (RCS Paris 989 493 200) — 11-person team across regulation, on-chain execution, DeFi research, sales and patrimonial advisory.
Quentin HOPP Founder & President (Paris).
https://www.linkedin.com/in/quentin-hopp/
 Master Banking & Finance KEDGE + AMF cert. Co-founder For Mining LTD (€10M+ revenue, 10,150+ machines, 500+ clients). Founder OMICRON 21 (€1.2M revenue).
Julien DAUBERT
https://www.linkedin.com/in/juliendaubert/
 Managing Director (Bordeaux). HEC Paris Executive. Founder/CEO 10H11 (data science for regulated fintechs, 14 years). GitHub:
https://github.com/judoh-collab
10H11.com
Saturnin PAULET
https://www.linkedin.com/in/saturnin-devins-8934a813a/
 Director of Strategy, Shareholder 25% (Lausanne, CH). Founder & Editor of Crypto & Macro newsletter (35,000+ subscribers).
Alexandre PELLENQ
https://www.linkedin.com/in/alexandrepellenq/
 CRCO / Risk & Compliance Director (Paris). PSCA compliance lead.
Pierrick BARNES
LinkedIn: in/pierrickbarnes ·
 CTO / CISO (Bordeaux). Soroban, Fireblocks MPC, Rails 8.1, Next.js 16. GitHub: [
https://github.com/Neoteddy-10h11
Marius](
https://github.com/Neoteddy-10h11￼￼Marius
) ORTIZ — Front-end Developer (Bordeaux) LinkedIn:
http://linkedin.com/in/mariusortiz
 · GitHub:
https://github.com/mariusortiz
 (8 repos)
Ahcène BENSALAMA — DeFi & Asset Management Expert (Paris). Sciences Po. Independent advisor with network across XAnge and the French crypto-investment ecosystem. Leads DeFi strategy curation and on-chain risk monitoring for the YieldVault allocator. LinkedIn: [
http://linkedin.com/in/ahcène-bensalama
Paul](
http://linkedin.com/in/ahcène-bensalama￼￼Paul
) ROCCHI — Supervisor of Advisory Function & Crypto-Asset Advisor (Paris, joining July 2026). Ph.D. Chemistry. 2+ years DeFi advisory at Montaigne Conseil & Patrimoine. Lecturer at Alyra (blockchain school) and INSEEC. LinkedIn: [
http://linkedin.com/in/paul-rocchi-defiscience
Cosmo](
http://linkedin.com/in/paul-rocchi-defiscience￼￼Cosmo
) DI GIUSEPPANTONIO — Head of Sales (Mulhouse). EM Strasbourg, ex-Uptoo. Building French CGPI channel since January 2026; LOIs signed. LinkedIn: [
http://linkedin.com/in/cosmo-di-giuseppantonio-4ab135182
Jules](
http://linkedin.com/in/cosmo-di-giuseppantonio-4ab135182￼￼Jules
) SCHILOVITZ — Inside Sales (Grenoble). Grenoble Ecole de Management. LinkedIn: [
http://linkedin.com/in/jules-schilovitz-b782941b3
Jaïmy](
http://linkedin.com/in/jules-schilovitz-b782941b3￼￼Jaïmy
) DOS SANTOS — Inside Sales (Kingersheim). Université de Lorraine. Speaker at I3 Belgium digital-asset conference. LinkedIn: [
http://linkedin.com/in/jaïmy-dos-santos-16b211214
Company](
http://linkedin.com/in/jaïmy-dos-santos-16b211214￼￼Company
):
https://www.for-yield
.com
ForYield
```
