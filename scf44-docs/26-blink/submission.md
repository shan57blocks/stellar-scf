# Blink: Blink for Merchants

Source: https://communityfund.stellar.org/submissions/recrSz2Ayxwr5axdC (SCF #44, awarded $75.0K, category: End-User Application)

- Website: https://useblinkapp.com
- Architecture doc: https://ablaze-cayenne-537.notion.site/Blink-for-Merchants-Architecture-36a3415dce1180b88b14eccde336276d

## Links in the submission

- https://ablaze-cayenne-537.notion.site/Blink-for-Merchants-Architecture-36a3415dce1180b88b14eccde336276d
- https://docs.google.com/spreadsheets/d/10-9ezWksU-UH-FNTldEWxYoZFwg3Ed_PB7gNVGjahGE
- https://docs.google.com/spreadsheets/d/1c6pb5iXEwFrTqfx40MJldBGpLyh_LoE3d-gbQuRoR7Q

## Submission text (as published)

```text
Products & Services
Customer Mobile App (iOS & Android):
Enables merchants and customers to send and receive crypto payments instantly over Bluetooth Low Energy (BLE). Designed for market stalls, kiosks, and everyday commerce across West Africa.
iOS
:
Download on TestFlight
Android
:
Download on Play Store
BLE Payment Layer
:
Powers proximity-based crypto transactions between devices using Bluetooth Low Energy. Customers and merchants don't need to share wallet addresses. BLE handles the device discovery and payment handshake, while the transaction settles on the Stellar network in real time.
Stellar Wallet Integration
:
Every user gets a non-custodial Stellar wallet at signup. Supports USDC with automated trustline setup, removing technical barriers from onboarding.
NGN Settlement Engine
:
Merchants receive payouts in Nigerian Naira regardless of the asset the customer pays with. Blink handles the USDC-to-NGN conversion and fiat off-ramp behind the scenes, so merchants never touch crypto directly.
Merchant Dashboard
:
Gives store owners a real-time view of transactions, earnings, and settlement history. Includes payment link generation, bulk payments disbursement and analytics for tracking daily sales volume.
Cross-Chain Payment Support (via NEAR Intents)
:
Customers holding USDC on Base, Solana, or Ethereum can pay seamlessly. NEAR Intents bridges funds to Stellar in the background merchants always see a clean NGN settlement.
Requested Budget
$75.0K
Traction Evidence
Since launching in April 2026, Blink has onboarded nearly 252 users and processed 2,360+ transactions totaling over ₦39,000,000 (~$28k USD).
We’ve spent $0 on marketing. Growth has been entirely organic through word of mouth, driven by users sharing Blink because it solves a real problem.
Blink has also gained traction within the Drips open-source community, where contributors increasingly use Blink to receive payouts.
User Evidence
:
https://docs.google.com/spreadsheets/d/10-9ezWksU-UH-FNTldEWxYoZFwg3Ed_PB7gNVGjahGE
Transaction Evidence
:
https://docs.google.com/spreadsheets/d/1c6pb5iXEwFrTqfx40MJldBGpLyh_LoE3d-gbQuRoR7Q
Dune
:
https://useblinkapp.com/dunes
Tranche 1 (Deliverable Roadmap) - MVP
Deliverable 1: Merchant Onboarding, Wallet Kit Integration & Checkout Widget/SDK
Description:
 Integrate Stellar Wallet Kits into the merchant registration flow to silently provision a Stellar wallet for every new merchant at signup. Merchant stores their bank account and settlement currency preference. Build the embeddable Blink Checkout Widget/SDK; a lightweight React/VanillaJS script/package that merchants drop into any website or Shopify store. At checkout, customers are shown a unique deposit address where they can send payment from any supported crypto wallet. Once the payment is confirmed, the transaction is automatically processed.
Measurement of Completion:
A merchant completes registration and a Stellar wallet is automatically created and linked to their account.
Merchant can view their public Stellar address and configure NGN as their settlement currency from the dashboard.
End-to-end widget flow: customer selects "Pay with Crypto" on a test storefront → get deposit address
Widget embeds cleanly via script tag or iframe.
Should be verifiable on testnet.
Budget:
$15,000
Timeline:
1–2 Weeks
Tranche 2 (Deliverable Roadmap) - Testnet
Deliverable 2: NEAR Intents Cross-Chain Integration, Liquidity Engine & NGN Fiat Settlement
Description:
 Implement NEAR Intents to enable seamless cross-chain payments from supported networks such as Base, Ethereum, and Solana. When a customer makes a payment, NEAR Intents routes and settles the transaction into the merchant's Stellar wallet as USDC. Once the USDC is received on Stellar, the Liquidity Engine locks the exchange rate, converts USDC to NGN, and initiates a bank transfer through Paystack. The merchant receives NGN directly in their bank account. The entire payment lifecycle—from cross-chain intent execution and Stellar settlement to fiat conversion and bank payout—is fully automated.
The full payment lifecycle — cross-chain bridging → Stellar detection → fiat payout — is automated end to end.
Measurement of Completion:
End-to-end testnet flow: customer selects "Pay with Crypto" on a test storefront → display deposit address → pays USDC on Base → NEAR Intents bridges funds → USDC lands in merchant Stellar wallet → dashboard reflects confirmed payment.
A test merchant receives a confirmed NGN bank credit following a USDC payment on testnet.
Conversion rate locked at time of payment. Paystack webhook confirms settlement. Transaction status progresses through PENDING → BRIDGED → SWAPPED → SETTLED in the dashboard.
Budget
$22,500
Timeline
2–6 Weeks
Tranche 3 (Deliverable Roadmap) - Mainnet
Deliverable 3: Stellar Disbursement Platform (SDP) & Mainnet Launch
Description:
 Integrate SDP for enterprise-grade bulk payouts, payroll, and mass refunds. Also Full production launch across all flows — checkout, cross-chain bridging, fiat settlement, and SDP bulk payouts — migrated from testnet to mainnet
Measurement of Completion
Merchant triggers a 10+ payee batch from the dashboard; all payees receive USDC/XLM on testnet with a compliance audit log generated and batch status reflected in the dashboard.
At least one real payment processed end-to-end on mainnet with confirmed NGN bank settlement.
Widget/SDK installable and functional, payment session created and checkout completed without leaving the merchant's storefront.
SCF professional user testing report delivered.
3 live merchant accounts with at least one confirmed mainnet transaction each.
Budget
$30,000
Timeline
Week 6–12
Team
Ebube Ebuka Onuora (CEO/Founder)
Ebube is the rare founder who has sat on both sides of the problem he is solving. Nearly 5 years inside the Nigerian banking system gave him an intimate understanding of exactly how and where the system fails ordinary people, knowledge that no classroom or accelerator program can teach. He carried that frustration into software development, completing ALX SE program, Utiva Full Stack bootcamp  and multiple blockchain programs before putting his skills to work through open source contributions on OnlyDust and Drips shipping real code in real Web3 projects long before Blink existed. That combination of financial sector experience and hands-on builder credibility is what earned him a $25,000 grant from the Starknet Foundation to build Fortichain and it is what makes him uniquely qualified to build the payment infrastructure Africa has always needed.
Linkedin
:
https://www.linkedin.com/in/onuoraebube
Github
:
https://github.com/ONEONUORA
Jethro Adamu (CTO)
Jethro Adamu is a Fullstack, Mobile, and Blockchain Developer with 8 years of experience building scalable Web2 and Web3 applications across Europe, the United States, and Africa. He has contributed to globally used products, including leading development at EcoHotels, where he managed a cross-functional team building a sustainable hotel booking platform for international travelers which generated 300k EUR last year in revenue, processed 160k bookings in total. He also led the development team of Nextup and built  high-performance microservices and mobile applications for Nextup, United States.
In the Web3 and fintech space, he engineered AutoRamp at TheBuidl Grid, a B2B platform enabling seamless fiat on-ramping and off-ramping infrastructure and also build Runescard, a crypto giftcard platform and has processed 3k giftcards during 6 months of operations.
Linkedin
:
https://www.linkedin.com/in/jaykosai/
Github
:
https://github.com/JayWebtech
Eleazar Musa (Lead Software Engineer)
Eleazar Shekoaga Musa is a Software and Blockchain Engineer with 5 years experience and a strong background in backend systems, decentralized infrastructure, and scalable application architecture. He has experience building Web2 and Web3 solutions across fintech, and payment infrastructure, contributing to projects focused on transparency, accessibility, and open digital ecosystems.
Eleazar has contributed to open-source projects within ecosystems, working on smart contract integrations, payment rails, wallet infrastructure, and developer tooling. He has contributed to open source projects such as Autoswapp and this reflects his focus on building interoperable systems that bridge traditional software engineering with decentralized technologies, he has developed and contributed to enterprise-grade applications including multi-chain wallets, blockchain payment SDKs, and public infrastructure solutions.
Linkedin
:
https://www.linkedin.com/in/eleazar-shekoaga-musa-09a70519a
Github
:
https://github.com/anonfedora
Ebube Ebuka Onuora
Eleazar Shekoaga Musa
Jethro Adamu
```
