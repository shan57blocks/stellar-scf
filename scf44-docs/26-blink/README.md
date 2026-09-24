# Blink (Blink for Merchants)

Blink is a mobile payment app from Nigeria. Customers pay shops with crypto (mainly USDC, a US-dollar stablecoin) by holding phones close together over Bluetooth, with a QR code as backup. Merchants get paid out in Nigerian naira (NGN) in their bank account, so they never hold crypto. The app launched in April 2026. It is for market stalls, kiosks and everyday shops in West Africa. The SCF #44 grant extends Blink to online merchants: a checkout widget for websites and Shopify stores, payments from other blockchains, and bulk payouts.

## What SCF #44 pays them to build

Total award: $75.0K (End-User Application). The three tranche budgets are 20%, 30% and 40% of the award ($67,500 in total). The other 10% is not tied to a tranche.

- **Tranche 1 – MVP ($15,000, 1–2 weeks):** merchant signup that automatically creates a Stellar wallet (via Stellar Wallets Kit). Merchants save bank details and choose NGN settlement. An embeddable checkout widget/SDK (React or plain JavaScript, script tag or iframe) that shows the customer a deposit address. Verifiable on testnet.
- **Tranche 2 – Testnet ($22,500, weeks 2–6):** cross-chain payments via NEAR Intents (a service that moves funds between blockchains): a customer pays USDC on Base, Ethereum or Solana, and it arrives as USDC in the merchant's Stellar wallet. A "Liquidity Engine" locks the rate, converts USDC to NGN and pays the bank through Paystack (a Nigerian payment processor). Status goes PENDING → BRIDGED → SWAPPED → SETTLED on the dashboard.
- **Tranche 3 – Mainnet ($30,000, weeks 6–12):** bulk payouts (payroll, refunds) using the Stellar Disbursement Platform (SDP, Stellar's open-source tool for mass payments), tested with a 10+ payee batch. Full mainnet launch, at least one real payment with NGN bank settlement, 3 live merchants, and the SCF user-testing report.

## How it works

- **Merchant dashboard and API** (Next.js front end). Analytics, payment links, API keys for store plugins, bulk payouts.
- **Checkout widget** (`blink-checkout-sdk` on npm). A merchant adds a button; it opens a pop-up (Paystack-style) where the customer pays. Funds settle to the merchant as Stellar USDC.
- **Wallets.** Each merchant gets a Stellar wallet at signup without handling seed phrases (the architecture doc calls it "custodial-lite"; the submission calls it non-custodial). Customers can connect their own wallets at checkout.
- **NEAR Intents.** Routes payments from Base, Solana or Ethereum into USDC on Stellar.
- **Rust "streamer"** watches Stellar for incoming payments.
- **Liquidity Engine and fiat off-ramp** (internal). Converts USDC to NGN and pays the bank via Paystack.
- **SDP** for bulk payouts.
- **Webhooks** from NEAR Intents, Paystack and SDP update payment status.
- Backend: Node.js/Express/TypeScript and Rust, PostgreSQL, Redis queues, hosted on Railway.

No custom Soroban smart contracts are described. Stellar is used for USDC settlement, trustlines, Wallets Kit and SDP.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recrSz2Ayxwr5axdC | submission.md | Tranches, budget, completion criteria |
| SCF project page | earlier SCF submission | https://communityfund.stellar.org/project/recaxGKpdc0HXu4XA | — | Lists only SCF #44; no earlier rounds |
| Blink for Merchants Architecture (Notion) | architecture | https://ablaze-cayenne-537.notion.site/Blink-for-Merchants-Architecture-36a3415dce1180b88b14eccde336276d | architecture.md | Text only; the 7 flow diagrams and data model are images, not transcribed |
| Pitch deck (HTML, 14 slides) | pitch | https://blink-pitch-deck-vert.vercel.app | pitch-deck.md | Not linked in the submission; from repo https://github.com/JayWebtech/blink-pitch-deck. Short 5-slide version at /pitch.html |
| "How to use Blink" guide | docs site | https://useblinkapp.com/doc | user-guide.md | Mobile app user guide; says developer SDK docs are "coming soon" |
| Checkout SDK README | docs | https://unpkg.com/blink-checkout-sdk/README.md | checkout-sdk-readme.md | npm package `blink-checkout-sdk`, latest 0.1.7 |
| Checkout SDK playground repo | demo | https://github.com/JayWebtech/blink-sdk-example | — | Live at https://blink-sdk-example.vercel.app/ |
| User evidence sheet | demo | https://docs.google.com/spreadsheets/d/10-9ezWksU-UH-FNTldEWxYoZFwg3Ed_PB7gNVGjahGE | — | Public; about 177 user rows, personal fields masked |
| Transaction evidence sheet | demo | https://docs.google.com/spreadsheets/d/1c6pb5iXEwFrTqfx40MJldBGpLyh_LoE3d-gbQuRoR7Q | — | Public; about 1,390 rows, masked |
| Dune dashboard page | demo | https://useblinkapp.com/dunes | — | Usage stats |
| Launch blog post | blog | https://useblinkapp.substack.com/p/blink-is-live-the-future-of-crypto | — | "Blink is live" |
| iOS app (TestFlight) | demo | https://testflight.apple.com/join/gNkuP7cP | — | |
| Android app (Play Store) | demo | https://play.google.com/store/apps/details?id=com.fortichain.blink | — | |
| Website | docs site | https://useblinkapp.com | — | |

## Gaps

- The main app, backend and merchant dashboard code is not public. Only the SDK playground and the pitch deck repos are open.
- The Notion architecture diagrams are images only; not transcribed.
- No written spec for the checkout API beyond the endpoint list in the architecture doc; developer docs are marked "coming soon".
- The submission says "Download on TestFlight / Play Store" without links; the links above come from the website.
- The tranches cover 90% of the award ($67,500 of $75.0K). The submission does not say how the other 10% is paid.
- No audit or demo video linked.
