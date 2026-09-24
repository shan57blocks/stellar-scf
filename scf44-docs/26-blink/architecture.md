Source: https://ablaze-cayenne-537.notion.site/Blink-for-Merchants-Architecture-36a3415dce1180b88b14eccde336276d

Note: text extracted from the public Notion page. The flow diagrams (Flows 1-6, lifecycle, data model) are images and are not transcribed here.

# Blink for Merchants Architecture

## Cross chain and Interoperability, Stellar Disbursement Platform & Stellar Wallet Kits
## Overview
This document provides a comprehensive architectural overview of Blink for Merchants, a crypto payment gateway designed to enable online businesses (Shopify stores, custom websites, mobile storefronts) to accept crypto payments globally and receive instant settlements in their local fiat currency.
Blink for Merchants abstracts away all crypto complexities (chains, tokens, bridges, wallets) for both the merchant and the end consumer, delivering a Stripe-like experience powered by the Stellar network and interoperability protocols.
## 1. High-Level System Architecture
Blink for Merchants is composed of a decoupled, distributed architecture that leverages external protocols for bridging and disbursement, while maintaining a robust internal engine for liquidity and fiat off-ramping.
### Core Ecosystem Components
1. Merchant Dashboard & API Gateway
  - The primary interface for merchants to view analytics, generate payment links, manage API keys for e-commerce integrations (e.g., Shopify, WooCommerce), and initiate disbursements.
1. NEAR Intents (Cross-Chain Interoperability Engine)
  - Acts as the normalization layer. It intercepts payments made in various supported assets across different blockchains (Base, Solana, Ethereum) and securely bridges them to USDC on the Stellar network.
1. Stellar Disbursement Platform (SDP)
  - The B2B disbursement backbone. Handles payroll, marketplace-style payouts, and bulk vendor settlements at scale, with built-in compliance and auditability.
1. Wallet Kits (Onboarding & Connect Layer)
  - Used for silent wallet provisioning during merchant onboarding and providing a seamless "Connect Wallet" (MetaMask, Phantom, etc.) UI for customers at checkout.
1. Liquidity Engine & Fiat Off-ramp (Internal)
  - Once funds land as USDC on Stellar, current infracstructure service performs the crypto-to-fiat conversion and communicates with fiat gateways to settle funds directly into the merchant's local bank account.
## 2. Integration Track & Technical Strategy
### A. Wallet Kits: Seamless Onboarding & Checkout
- Merchant Side (Silent Provisioning): Most merchants do not have crypto wallets. During the traditional signup flow, Wallet Kits are used to silently spin up a custodial wallet infrastructure. The merchant becomes "wallet-ready" without managing seed phrases or keys.
- Customer Side (Native Connection): At checkout, Wallet Kits provide a modal allowing crypto-native customers to connect their existing wallets across any supported chain to authorize payments seamlessly.
### B. NEAR Intents: Cross-Chain Liquidity Routing
- Problem: Customers hold liquidity across fragmented ecosystems (Ethereum, Base, Solana), while Blink standardizes settlement on Stellar for speed and low fees.
- Solution: NEAR Intents is integrated at the checkout layer. When a customer pays in USDC on Base, the transaction is routed through NEAR Intents, bridged, and finalized as USDC on Stellar in Blink's merchant settlement wallet.
- Benefit: The merchant is never exposed to cross-chain complexity; they only see a successful payment and local currency settlement.
### C. Stellar Disbursement Platform (SDP): Large Scale Payouts
- Use Case: Marketplaces paying multiple vendors, payroll, or mass refunds.
- Integration: Merchants interact with the Blink Dashboard to trigger bulk payouts. The Blink Backend formats this data and feeds it into the SDP. SDP handles the mass distribution on the Stellar network, ensuring compliance checks, tracking, and scalable throughput.
## 4. Proposed Folder Structure
```
src/
├── core/                        # Global configs, middleware, guards, errors
├── infrastructure/              # External provider integrations
│   ├── near-intents/               # Near Intents SDK and bridging utilities
│   ├── paystack/                # Fiat payout and bank resolution logic
│   ├── sdp/                     # Stellar Disbursement Platform client
│   ├── stellar/                 # Native Stellar SDK interactions
│   └── wallet-kit/              # Wallet provisioning logic
├── modules/                     # Domain-specific business logic
│   ├── disbursements/           # Bulk payouts and SDP mapping
│   ├── liquidity/               # Crypto-to-fiat conversion rates & execution
│   ├── merchants/               # Onboarding, KYC, profile, and API key management
│   ├── payments/                # Checkout sessions, tracking, and resolution
│   └── wallets/                 # Merchant wallet references and balance tracking
└── webhooks/                    # Dedicated handlers for async events
	├── near-intents.webhook.ts     # Listens for bridged funds arrival on Stellar
	├── paystack.webhook.ts      # Listens for fiat bank settlement success
	└── sdp.webhook.ts           # Listens for bulk payout batch completion
```
## 4. System Flow Architectures
This section details the operational sequences that power the entire ecosystem, from frontend initialization to security and backend processing.
### Flow 1: Frontend Widget & Checkout Initialization
How a 3rd-party e-commerce site securely generates a payment session and loads the Blink Widget.
[image: attachment:1052aa04-a15e-4d5f-95c0-982c22a3187e:image.png]
### Flow 2: Merchant Onboarding (Wallet Kits)
The onboarding flow requires zero prior crypto knowledge from the merchant.
[image: attachment:e4abcaa9-b311-4f7a-a65c-f68c42dbb1f2:image.png]
### Flow 3: Cross-Chain Customer Checkout (NEAR Intents)
The checkout process allows a customer to pay from any chain while ensuring the Blink ecosystem receives standard Stellar USDC, followed by an immediate fiat payout.
[image: attachment:5abde0be-f610-48e7-9fcd-12ae0af344e9:image.png]
### Flow 4: Bulk Disbursement & Payroll (SDP)
For enterprise merchants or marketplaces needing to disburse funds to multiple parties simultaneously.
[image: attachment:9360491f-32e1-4215-b8d8-75b177134b4b:image.png]
### Flow 5: Security & Custodial-Lite Key Management
How silent wallets are provisioned and utilized without ever exposing private keys to persistent storage or potential memory leaks.
[image: attachment:09566e13-6593-4f56-9708-49f0918b54ba:image.png]
### Flow 6: Async Webhook Processing
How the system safely captures asynchronous events from external providers to guarantee idempotent execution.
[image: attachment:a6846002-7951-4ade-ac6f-1454c14c70a4:image.png]
### Flow 4: The Complete Payment & Settlement Lifecycle (Cross-Chain -> Detection -> Liquidity -> Fiat)
This defines the full lifecycle. It incorporates NEAR Intents for cross-chain liquidity, the isolated Rust microservice for detection on Stellar, and the Liquidity Engine for final fiat payout.
[image: attachment:3611068b-355a-4c77-8ce2-729b0a404750:image.png]
## 5. Unified Data Model
The data architecture acts as the single source of truth connecting web3 wallets to web2 fiat accounts.
[image: attachment:6d9c8a44-b274-4ddc-980b-f6aa152a4f63:image.png]
## 6. Core API Endpoints
These represent the primary REST interfaces for the merchant dashboard, external integrations (e.g., e-commerce plugins), and webhooks.
### A. Merchant Onboarding & Settings
- `POST /api/v1/merchants/register` - Registers a merchant and triggers the Wallet Kit to silently provision their receiving infrastructure.
- `GET /api/v1/merchants/wallet` - Retrieves the merchant's public receiving address and balances.
- `POST /api/v1/merchants/api-keys` - Generates API keys for external integrations (e.g., for their Shopify plugin).
### B. Checkout & Payments (Public API)
- `POST /api/v1/checkout/sessions` - Initiates a payment session. Returns a hosted checkout URL where Wallet Kits are injected, allowing the customer to connect their wallet.
- `GET /api/v1/checkout/sessions/:id` - Polls the status of an ongoing checkout session.
- `GET /api/v1/payments/history` - Fetches the merchant's incoming payment history.
### C. Liquidity & Fiat Off-ramp
- `GET /api/v1/liquidity/rates` - Gets the real-time crypto-to-fiat conversion rate.
- `POST /api/v1/liquidity/withdraw` - Manual trigger for a merchant to convert held USDC to local fiat in their bank.
### D. Bulk Disbursements (SDP)
- `POST /api/v1/disbursements/batch` - Uploads a list of payees (CSV/JSON) to trigger a mass payout via the SDP.
- `GET /api/v1/disbursements/batch/:id` - Checks the status and compliance audit of a bulk payout batch.
### E. Webhook Receivers
- `POST /api/v1/webhooks/near-intents` - Receives confirmations from NEAR Intent when a cross-chain swap lands successfully on Stellar.
- `POST /api/v1/webhooks/paystack` - Receives confirmations when the fiat off-ramp successfully credits the merchant's bank account.
- `POST /api/v1/webhooks/sdp` - Receives status updates for each individual payee within a bulk disbursement batch.
## 7. Technology Stack Summary
Blink utilizes a modern, typed, and scalable stack to ensure both developer velocity and enterprise-grade reliability.
### Core Stack
- Frontend (Merchant Dashboard): Next.js, React, TailwindCSS.
- Checkout Widget: Lightweight React/VanillaJS embeddable script.
- Backend APIs & Liquidity Engine: Rust, Node.js, Express & TypeScript.
- Database ORM: Prisma ORM.
### Data & State Management
- Primary Database: PostgreSQL (relational integrity for financial data).
- Caching & Queues: Redis (Idempotency locking, BullMQ message queues, and rate-limiting).
## 8. Infrastructure & Deployment
Blink operates on a highly available cloud architecture, utilizing Railway as its primary Platform-as-a-Service (PaaS) to ensure seamless scaling and deployment.
### Railway Ecosystem Architecture
- Continuous Deployment (CI/CD): Native GitHub integration pushes zero-downtime deployments to Railway on `main` branch merges.
- Node.js API Instances: The main backend and Liquidity Engine scale horizontally on Railway to handle sudden spikes in checkout sessions and webhook traffic.
- Rust Streamer: Deployed as an isolated, lightweight Railway worker service. Its low memory footprint and high concurrency make it incredibly cheap and efficient to run 24/7.
- Managed Databases: Both PostgreSQL and Redis are provisioned directly within the Railway private network. This ensures internal API calls to the database have sub-millisecond latency and are never exposed to the public internet.
## 9. Summary & Value Proposition
The Blink for Merchants architecture creates a powerful illusion of simplicity. On the surface, it behaves exactly like traditional, localized payment processors (e.g., Stripe, Paystack)—accepting payments and depositing local fiat into a bank account.
However, underneath, it utilizes an advanced web3 orchestration layer:
- Wallet Kits ensure zero-friction onboarding.
- NEAR Intents prevents ecosystem lock-in, capturing liquidity from any major chain.
- Stellar provides the high-speed, low-cost settlement backbone.
- SDP expands the product suite into B2B payouts and enterprise payroll.
- Liquidity Engine closes the loop by automatically off-ramping to fiat.
