# TroqPay: Sell with Pix. Receive in USD.: TroqPay: Sell with Pix. Receive in USD.

Source: https://communityfund.stellar.org/submissions/recidiiEnQNycc3LA (SCF #44, awarded $60.0K, category: End-User Application)

- Website: https://troqpay.com
- Architecture doc: https://docs.google.com/document/d/1EQa00t9Hmn3KvYZCTfonQgaTiQ5euqTi/edit?usp=sharing&ouid=102341337188527212430&rtpof=true&sd=true

## Links in the submission

- https://docs.google.com/document/d/1EQa00t9Hmn3KvYZCTfonQgaTiQ5euqTi/edit?usp=sharing&ouid=102341337188527212430&rtpof=true&sd=true
- https://drive.google.com/drive/folders/1lqU6xWZp2AJaK6fVubzF9IQzVni3gRD5?usp=sharing
- https://www.youtube.com/@ovitorpio

## Submission text (as published)

```text
Products & Services
TroqPay is a payments product for Brazilian registered-company merchants, already live in production. Merchants can accept Pix in BRL through API, hosted checkout, payment links and POS, while managing payments, balances, digital-dollar access and AI-assisted merchant analytics in one platform. The product runs with production-access controls and strict TEST/LIVE separation.
Today, TroqPay supports Pix collection, a per-merchant BRL ledger and stablecoin payouts through a local VASP/OTC provider. This build migrates TroqPay’s current stablecoin payout path from USDT wallet payouts to native USDC on Stellar, delivered to merchant-controlled wallets created through Privy.
Stellar becomes the digital-dollar settlement and balance layer. A merchant will be able to settle Pix proceeds into native USDC on Stellar, hold that USDC in their own merchant-controlled wallet, move it back to BRL by Pix through the selected regulated on/off-ramp provider, and opt into DeFindex for idle USDC. TroqPay coordinates the request, prepares transaction flows, records provider and on-chain evidence, and reconciles every leg. The merchant holds the signing key; the selected provider handles regulated BRL <> USDC conversion and KYC/KYB where applicable.
Pix is fast, but it ends at Brazil’s border and at the BRL balance. Stellar makes this merchant workflow practical by providing fast settlement, low and predictable network fees, native USDC balances and merchant-controlled wallets. This is the gap the build closes: turning Pix merchant proceeds into a productized USDC on Stellar settlement path.
Onboarding uses Privy for seedless, non-custodial Stellar wallets, so merchants can hold USDC without managing seed phrases. A Stellar passkey smart wallet remains the contingency for deeper Soroban signing needs. The yield module integrates DeFindex’s existing vault infrastructure and requires merchant opt-in.
Requested Budget
$60.0K
Traction Evidence
TroqPay runs a working Pix payments product in production. Merchants can accept Pix online or in person through payment links, API, hosted checkout and POS, while managing payments, balances, digital-dollar access and AI-assisted merchant analytics in one platform. The product operates behind production-access controls and strict TEST/LIVE separation.
TroqPay also has an existing stablecoin payout path. Through a local VASP/OTC provider, a merchant can quote BRL into USDT, have the provider execute the trade, and receive USDT at a wallet they control. The grant-funded work migrates this stablecoin payout path from USDT wallet payouts into native USDC on Stellar, delivered to merchant-controlled Privy wallets.
Before launching the current TroqPay platform, the team operated a peer-to-peer and OTC USDT/BRL flow at approximately BRL 2 million per month, equivalent to around USD 400k per month. This prior operating volume is not presented as current TroqPay app TPV; it is evidence of real demand, operational experience and an existing commercial base around Brazilian BRL/stablecoin flows.
Those OTC and stablecoin customers are now part of TroqPay’s migration pipeline. The team is converting existing OTC relationships and merchant pilots into the new TroqPay platform, where Pix collection, merchant balances, digital-dollar access and future USDC on Stellar settlement are handled in a productized, auditable and scalable flow.
Redacted supporting evidence is included with the submission including dashboards, transactions, and evidence of prior OTC/P2P operating volume.
This traction is the reason the grant is structured as an integration project, not a research project. The technical risk is not whether Brazilian merchants need Pix-to-stablecoin flows; the team has already operated that demand. The grant work is to migrate this existing stablecoin payout demand into a productized Stellar flow using native USDC, Privy wallets, an on/off-ramp provider and DeFindex.
The tranches below show how we will move from standalone MVP, to TroqPay testnet integration, to capped mainnet launch with real Pix merchant volume and verifiable Stellar transaction evidence.
Supporting Evidence
OTC TPV evidence:
https://drive.google.com/drive/folders/1lqU6xWZp2AJaK6fVubzF9IQzVni3gRD5?usp=sharing
TroqPay
https://troqpay.com
Instagram:
https://www.instagram.com/troqpay/
LinkedIn:
https://www.linkedin.com/company/troqpay/
Web Summit Rio 2026:
https://rio.websummit.com/pt-br/appearances/rio26/58152bfd-2a4a-4e50-96c9-37ee89270fe2/troqpay/
Public product
Production App:
https://app.troqpay.com/
Vitor Pio Social Proof
YouTube:
https://www.youtube.com/@ovitorpio
Instagram:
https://www.instagram.com/ovitorpio/
Tranche 1 (Deliverable Roadmap) - MVP
Tranche #1 - Standalone Stellar MVP - USD 12,000 (20%) - PaltaLabs Primary Owner
Outcome.
 Ship a working standalone Stellar MVP outside the TroqPay production app before embedding it into TroqPay. This tranche validates one merchant account, Privy wallet onboarding, USDC balance experience, on-ramp into native USDC on Stellar, DeFindex deposit and withdraw, and off-ramp back through on/off-ramp provider.
This tranche is intentionally standalone. No TroqPay production-repository changes are required for Tranche #1. TroqPay participates through product requirements, technical supervision, review, and integration learning so the validated MVP can be embedded into TroqPay during Tranche #2.
Deliverables and verification:
Working standalone MVP.
Scope: merchant account, Privy wallet, USDC balance, on-ramp, off-ramp, DeFindex deposit and DeFindex withdraw.
Verification: recorded demo plus testnet/sandbox transaction hashes, provider references, or SDK logs for each leg.
On-ramp into native USDC on Stellar.
Scope: BRL-to-USDC test/sandbox flow through Alfred or the selected on/off-ramp provider, with native USDC delivered to the merchant-controlled Stellar wallet.
Verification: provider sandbox reference and Stellar testnet transaction evidence.
Off-ramp from USDC on Stellar back to BRL.
Scope: merchant signs or authorizes the required USDC flow back through on/off-ramp, and the provider returns BRL through the off-ramp path.
Verification: provider sandbox reference, Stellar payment evidence, and off-ramp status evidence.
DeFindex deposit and withdraw round-trip.
Scope: merchant signs a DeFindex deposit and withdraw/redeem flow from the Privy-connected Stellar wallet.
Verification: testnet transaction hash or SDK evidence for deposit and withdraw.
Wallet-signing decision record.
Scope: document the selected signing path for DeFindex interactions, including whether Privy alone is sufficient or whether a passkey-based fallback is required.
Verification: one-page ADR supported by a merchant-signed DeFindex testnet round-trip.
Integration handoff documentation for TroqPay engineering.
Scope: implementation notes covering wallet setup, signing path, provider assumptions, DeFindex flow, open issues, and Tranche #2 integration requirements.
Verification: handoff document delivered to TroqPay engineering.
Recorded end-to-end MVP demo.
Scope: merchant onboarding → Privy wallet → USDC balance → on-ramp → DeFindex deposit → DeFindex withdraw → off-ramp.
Verification: recorded walkthrough.
Public signing reference.
Scope: publish a public technical reference for the selected Stellar wallet-to-DeFindex signing path, without exposing proprietary TroqPay payment code.
Verification: public repository, README, technical note, or public reference with testnet evidence.
PaltaLabs cost mapping.
 PaltaLabs quoted USD 20,000 for the standalone MVP and included 16h of support through the end of Tranche #3. TroqPay will pay PaltaLabs from SCF grant proceeds in installments across the project. Payment #0 and Tranche #1 allocate USD 18,000 toward MVP mobilization and delivery. The remaining USD 2,000 is allocated from later SCF payments for PaltaLabs’ included support during Tranche #2 and Tranche #3.
Tranche 2 (Deliverable Roadmap) - Testnet
Tranche #2 - Productized Testnet Integration - USD 18,000 (30%) - TroqPay Primary Owner
Primary owner:
 TroqPay engineering
Support:
 PaltaLabs support and code review from the included 16h support package
Estimated effort:
 approximately 300 TroqPay engineering hours
Budget allocation:
 approximately USD 17,000 for TroqPay engineering and USD 1,000 allocated to PaltaLabs support from the included support package
Outcome.
 Embed the standalone Tranche #1 MVP flow into the TroqPay merchant app and API in testnet/test mode, behind the TROQPAY_USDC_STELLAR_ENABLED feature flag. 
Tranche #2 starts the TroqPay in-repo implementation: the USDC_STELLAR rail, provider dispatch, Privy onboarding, wallet registry, quote and validation, ledger records, testnet DeFindex flow, dashboard states, and reconciliation.
Deliverables and verification:
USDC_STELLAR rail added behind feature flag.
Scope: add the new rail behind TROQPAY_USDC_STELLAR_ENABLED, defaulting to false.
Verification: code merged, flag-off regression test passes, and the existing Pix collection, BRL ledger, BRL Pix payout path, and current USDT payout flow remain unchanged when the flag is off.
Withdrawal decision points updated for USDC_STELLAR.
Scope: update the relevant rail-specific branch sites in the withdrawal service, including settlement evidence, manual fallback, quote, validation, destination handling, provider handoff, processing guard, and exhaustiveness checks.
Verification: branch-site checklist completed and reviewed.
Provider dispatch by rail implemented.
Scope: introduce provider dispatch so USDC_STELLAR can use on/off-ramp provider through the existing payout provider interface.
Verification: sandbox provider reference is stored with the settlement record and linked to the corresponding withdrawal ID.
Privy onboarding embedded in TroqPay merchant app.
Scope: merchants in the USDC_STELLAR test flow receive a merchant-controlled Stellar wallet through Privy. External arbitrary wallet addresses are not used for this rail.
Verification: reproducible demo merchant completes Privy onboarding inside TroqPay and has a Stellar wallet associated with the merchant account.
Idempotent Stellar account and USDC trustline setup.
Scope: implement a setup process tied to merchant account, Stellar address, and network. The process verifies account activation, verifies the USDC trustline where required, and completes required setup before settlement is enabled.
Verification: demo merchant wallet is active on testnet, has the required USDC capability, and the setup process can be retried without duplicating state.
Canonical USDC_STELLAR destination stored.
Scope: store stellarWalletAddress or equivalent canonical destination metadata for the merchant’s Privy-connected Stellar wallet.
Verification: wallet registry row and merchant payout settings show the Privy-connected Stellar wallet as the USDC_STELLAR destination.
Quote and validation for BRL to USDC_STELLAR.
Scope: implement quote and validation checks for the new rail, including Stellar address format, wallet status, account status, USDC capability, merchant ownership of the destination, and available BRL balance.
Verification: unit tests pass and a test quote is returned for a reproducible demo merchant.
Testnet on-ramp settlement stored end to end.
Scope: run a sandbox/testnet BRL-to-USDC settlement through Alfred or the selected on/off-ramp provider, with native USDC delivered to the merchant’s Privy-connected Stellar wallet.
Verification: provider sandbox reference, Stellar transaction hash, ledger movement, settlement status, and merchant wallet destination are stored.
DeFindex testnet flow embedded in TroqPay.
Scope: enable a testnet DeFindex deposit and withdraw/redeem flow from the merchant’s Privy-connected Stellar wallet, with explicit merchant action and wallet signature.
Verification: signed testnet transaction evidence or SDK logs for deposit and withdraw, plus vault-position readback in TroqPay.
Merchant dashboard states implemented.
Scope: show the settlement lifecycle states in the TroqPay merchant app and/or internal dashboard: requested, reserved, partner_pending, on_chain_submitted, completed, failed, and reconciled.
Verification: recorded walkthrough showing each state on a reproducible demo merchant account.
Testnet reconciliation report generated.
Scope: compare TroqPay ledger obligations against Stellar wallet balances and DeFindex vault position for the demo merchant.
Verification: generated reconciliation report and recorded walkthrough, referencing testnet wallet/vault addresses and transaction hashes that can be checked by reviewers.
Tranche 3 (Deliverable Roadmap) - Mainnet
Tranche #3 - Capped Mainnet Launch and UX Readiness - USD 24,000 (40%) - TroqPay Primary Owner
Primary owner:
 TroqPay engineering
Support:
 PaltaLabs review as needed from the included 16h support package
Estimated effort:
 approximately 420 TroqPay engineering hours
Budget allocation:
 approximately USD 23,000 for TroqPay engineering and USD 1,000 allocated to PaltaLabs support from the included support package
Outcome.
 Launch a capped mainnet pilot that brings real Pix merchant volume into native USDC on Stellar, delivered to merchant-controlled Privy wallets, with DeFindex opt-in yield, operational monitoring, on-chain reconciliation, UX readiness, and rollback controls.
Deliverables and verification:
USDC_STELLAR enabled in LIVE for approved merchants only.
Scope: enable the rail only for approved registered-company merchants using allowlist, transaction caps, operational review, and emergency disable controls.
Verification: live configuration, allowlist evidence, caps, and emergency disable procedure documented.
At least one capped real Pix to USDC mainnet settlement completed.
Scope: a merchant receives Pix in BRL, requests USDC settlement, and the regulated on/off-ramp provider delivers native USDC on Stellar to the merchant’s Privy-connected wallet.
Verification: provider reference, BRL ledger movement, mainnet Stellar transaction hash, merchant wallet address, and settlement record.
Stablecoin payout migration path validated.
Scope: validate the migration from the current USDT wallet payout path to USDC_STELLAR for eligible stablecoin payouts.
Verification: current USDT payout flow remains unaffected while the flag is off; capped pilot transactions confirm that USDC_STELLAR can become the stablecoin payout path after validation.
Stellar transaction confirmation watcher live.
Scope: confirm Stellar transaction finality and attach transaction hash and confirmation status as settlement evidence.
Verification: operational record shows transaction hash, confirmation status, finality check, settlement status, and associated withdrawal ID for pilot transactions.
On-chain reconciliation and proof-of-funds dashboard active.
Scope: compare TroqPay ledger obligations against Stellar wallet balances and DeFindex vault positions on a scheduled basis.
Verification: dashboard walkthrough and reconciliation report for pilot transactions, with zero unexplained variance and references to the same mainnet transaction hashes, wallet addresses, and vault addresses submitted as evidence.
DeFindex deposit and withdraw live on mainnet.
Scope: enable opt-in DeFindex deposit and withdraw/redeem for approved pilot merchants, with explicit merchant consent and wallet signature.
Verification: signed mainnet DeFindex deposit and withdraw transaction hashes, consent UX evidence, and vault-position readback.
Failed or partial settlement recovery path implemented.
Scope: implement and test recovery behavior for failed, delayed, or partial provider/on-chain settlement states.
Verification: recovery scenario tested; retry behavior is idempotent and does not create duplicate provider requests or duplicate payout attempts.
Operational monitoring implemented around USDC_STELLAR.
Scope: each settlement has a traceable operational record containing withdrawal ID, merchant ID, provider reference, provider status, Stellar transaction hash, confirmation status, ledger movement, reconciliation status, and failure reason where applicable.
Verification: pilot settlement records show pending, delayed, failed, completed, and reconciled states where applicable.
UX readiness completed.
Scope: merchant-facing onboarding, wallet status, settlement request, USDC balance, DeFindex opt-in, settlement status, and off-ramp screens are functional and tested.
Verification: recorded end-to-end demo of merchant onboarding → Pix collection → USDC on Stellar settlement → DeFindex opt-in deposit → DeFindex withdraw → off-ramp.
Go-live metrics report delivered.
Scope: report measurable Stellar ecosystem impact from the pilot.
Verification: report includes USDC on-ramped, retained USDC, DeFindex TVL attributable to TroqPay merchants, active merchants on the Stellar rail, relevant wallet/vault addresses, and transaction hashes.
Launch and rollback runbooks delivered.
Scope: document activation, merchant allowlist, caps, monitoring, failed settlement handling, emergency disable procedure, rollback plan, and go-live decision process.
Verification: runbooks delivered and go-live decision recorded.
Budget Alignment Statement
Every dollar maps to a Stellar-integrated deliverable: standalone MVP implementation, Privy onboarding, USDC_STELLAR rail, on/off-ramp provider integration, DeFindex integration, frontend flows, testnet and mainnet demos, operational monitoring, reconciliation, runbooks, documentation, and launch readiness.
The grant-funded budget totals USD 60,000:
USD 20,000 for PaltaLabs’ standalone MVP and included support through Tranche #3;
USD 40,000 for TroqPay’s in-house engineering, product integration, testing, operational readiness, reconciliation, dashboard work, and capped mainnet launch.
Team
TroqPay is led by a multidisciplinary team with experience across software, cybersecurity, blockchain communities, product, operations, and go-to-market.
Vítor Pio, founder and CEO, is a technical builder with experience in cybersecurity, privacy, product, and applied technology. He is a former Samsung R&D Security Engineer, holds an MSc in Computer Science from Universidade Federal Fluminense, and has academic work in quantum computing.
LinkedIn:
https://www.linkedin.com/in/pio/
Jamile Lima Pio, COO, leads operations, partnerships, customer support, and go-to-market. She has experience in HR, innovation, processes, governance, and product operations, including previous experience at CI&T, a NYSE-listed technology company.
LinkedIn:
https://www.linkedin.com/in/jamilelimapio/
Jamile Lima Pio
Vitor Pio
```
