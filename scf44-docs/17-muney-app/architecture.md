Source: https://drive.google.com/file/d/1MswA_78S2yarWKlkW0336TOtoKgzOcGn/view?usp=sharing

Transcribed from the image-only PDF (local copy: architecture.pdf, 3 pages of diagrams). Arrows are described as "A → B (label)". A cleaner text version of the same design lives in the repo: repo-architecture.md.

# STELLAR - MUNEY RAILS

## Page 1 — Muney + Stellar Technical Architecture

- Document: Muney + Stellar Technical Architecture
- Version: 1.0
- Created: June 12, 2026
- Description: Technical architecture for USDC on Stellar cash-in/out in El Salvador, connecting partner wallets, Muney's orchestration platform, and its authorized merchant network through the User SDK and Merchant SDK.

Root node: "Muney + Stellar Technical Architecture — El Salvador Cash-In/Out Network".

Summary notes:
- Stellar settles digitally; Muney orchestrates merchants, compliance, lifecycle, and reconciliation.
- El Salvador pilot 100 merchants; scales toward 500.
- Legend: green = USDC, orange = cash, black = API/data, gray dashed = events, orange = compliance, purple = new SCF, teal = existing Muney.

Layers:
- A. End User Layer
- B. Partner Wallet / Fintech / Money Transmitter Layer
  - End user with partner wallet → (use wallet) → "Request cash-in/out + select merchant + view status + show QR/code + receive/deposit cash"
  - Note: no direct blockchain interaction for the end user.
  - → (API request) → Partner mobile app → (uses SDK) → Muney User SDK
  - Partner backend + auth + webhook receiver + history
- C. Muney Platform Layer (existing Muney, teal)
  - Muney User SDK: orders, merchants, quotes, status, QR/code → (create order) → API Gateway; → (get quotes) → Quote & Fee Engine
  - API Gateway: auth, rate limits, validation, idempotency → (route request) → Orchestration Engine
  - Orchestration Engine: orders + lifecycle state machine
    - → (quote lookup) Quote & Fee Engine: rates, fees, expiry, final amount
    - → (compliance) Compliance check → Pass → Routing & Liquidity; Review/fail → "Failed / expired / refunded / manual review"
    - → (merchant search) Routing & Liquidity: merchant assignment + cash checks → (assign merchant) Merchant Ops Dashboard
    - → (settle USDC) Stellar USDC Settlement Adapter
    - → (record entry) Ledger & Reconciliation: balances, refs, exports → (reconcile) Merchant Ops Dashboard, Partner Dashboard
    - → (backend sync) Partner backend
  - Webhooks & Notifications → (dashed status) Partner backend; events come from Routing and the Stellar network
- D. Stellar Integration Layer — New Stellar Integration / SCF Build Scope (purple)
  - Stellar USDC Settlement Adapter → (sign tx) Secure Signing Service: keys never in SDKs
  - Stellar USDC Settlement Adapter → (submit tx) Stellar Network: USDC on Stellar → (confirm hash) Ledger & Reconciliation
  - Stellar USDC Settlement Adapter → (disburse) Stellar Disbursement Platform
  - Stellar USDC Settlement Adapter → (anchor flow) Anchor Platform: SEP-6/24/31 + compliance coordination
  - Note: USDC on-chain; cash via merchants; personal/compliance data off-chain.
- E. Merchant Cash Network Layer
  - Merchant SDK / SSDK: auth, assigned txs, scan QR/code, confirm cash (receives assigned tx events; runs on merchant device: phone / tablet / POS)
  - → (merchant action) Authorized Muney Merchant: verify user/code, provide/receive cash → (cash delivered/received) Transaction completed
  - Merchant Ops Dashboard → (manage network) Authorized Muney Merchant
  - Network scale: 100 merchants to 500
- F. Compliance, Security & Observability
  - Compliance: KYC/KYB/KYT, screening, limits, risk, manual review (→ check rules on Compliance check)
  - Security: encryption, RBAC, signed webhooks, audit logs, key mgmt (→ protect API Gateway and Signing Service)
  - Observability: logs, metrics, alerts, liquidity monitoring, exceptions (→ monitor Orchestration Engine and Settlement Adapter)

## Page 2 — Muney + Stellar Cash-In Flow

- Version 1.0, created June 12, 2026.
- Description: Technical sequence for receiving cash through an authorized merchant and settling USDC on Stellar into the user's partner wallet in El Salvador.
- Participants: End User, Partner Wallet, User SDK, Muney API Gateway, Merchant Routing Engine, Merchant SDK, Authorized Merchant, Compliance Engine, Stellar USDC Adapter, Stellar Network, Muney Ledger/Webhooks, Manual Review Queue, Dispute Resolution Process, Refund Processor.

Steps:
1. End user selects Cash In (partner wallet).
2. Request quote and nearby merchant (User SDK).
3. Authenticate and validate request (API Gateway).
4. Validate user, partner, limits, and merchant availability.
5. Find available merchant with cash-in capability (Merchant Routing Engine).
6. Create cash-in order and return merchant location, QR/code, and expiration time.
7. Display merchant info and QR/code.
8. Show cash-in details to user; 10 min cash handoff window.
9. User visits physical merchant location with cash.
10. Merchant scans QR code or enters one-time transaction code; verify transaction details.
11. User hands cash to merchant.
12. Merchant confirms cash received.
13. Merchant SDK sends cash received confirmation to API Gateway.
14. Trigger final compliance checks.
15. Compliance Engine performs KYT, sanctions screening, and risk scoring.
16. Return approval.
17. Instruct Stellar USDC Adapter to create settlement transaction.
18. Create USDC transfer transaction.
19. Submit transaction to Stellar Network.
20. Confirm transaction on-chain (real-time Stellar settlement ~5 sec).
21. Detect confirmation and validate transaction hash.
22. Notify successful settlement.
23. Update internal records and reconciliation.
24. Credit merchant account with fee.
25. Send signed status update to Partner Wallet.
26. Display USDC credited / cash-in completed.

Exceptions:
- A: Merchant confirmation mismatch → manual review queue.
- B: Compliance review required → pending_manual_review.
- C: Stellar transaction failure → retry logic, then manual resolution.
- D: Duplicate confirmation detected → prevent double credit, flag for review.
- E: Incorrect amount received → dispute resolution process.
- F: Refund scenario → reverse flow and return cash to user.

## Page 3 — Muney + Stellar Cash-Out Flow

- Version 1.0, created June 12, 2026.
- Description: Technical sequence for converting USDC on Stellar into local cash through a partner wallet, Muney's orchestration platform, and an authorized merchant in El Salvador.
- Participants: End User, Partner Wallet, User SDK, Muney API Gateway, Compliance Engine, Stellar USDC Adapter, Stellar Network, Merchant Routing Engine, Merchant SDK, Authorized Merchant, Muney Ledger/Webhooks, Refund Process, Manual Review Queue, Dispute Resolution.

Steps:
1. End user selects Cash Out.
2. Request quote and nearby merchant locations.
3. Send authenticated request.
4. Validate partner, user, amount, limits, merchant availability.
5. Perform KYC/KYT screening and risk checks; 5a. return compliance result.
6. Find available merchant with sufficient cash; 6a. return merchant availability and routing result.
7. Create order and return fees, expiration time, merchant location, QR/code.
8. Display merchant info and QR/code.
9. Initiate USDC transfer on Stellar; 9a. submit transfer via Stellar USDC Adapter.
10. Detect incoming USDC transaction.
11. Confirm transaction on-chain.
12. Validate transaction hash and amount.
13. Update order status to ready_for_cash.
14. Notify assigned transaction to Merchant SDK.
15. User visits physical merchant location.
16. Scan QR code or enter one-time transaction code.
17. Verify user identity and transaction details; 17a. return verification result.
18. Hand cash to user.
19. Confirm cash delivery.
20. Update internal records and reconciliation.
21. Credit merchant account with fee.
22. Send signed status update.
23. Display cash-out completed.

Exceptions:
- A: No Stellar payment before expiration → order expires and triggers refund.
- B: Merchant lacks sufficient cash → reassign to different merchant.
- C: Compliance review required → set order status pending_manual_review.
- D: User does not appear before timeout → transaction expires after timeout.
- E: Cash confirmation dispute → manual resolution queue processing.

Timing: 15 min expiration window; real-time Stellar confirmation ~5 sec.
