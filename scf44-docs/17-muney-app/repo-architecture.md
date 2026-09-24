Source: https://gitlab.com/holamuney/muney-stellar-integration/-/blob/main/docs/architecture.md

# Muney × Stellar — Technical Architecture

**Version:** 1.1 · **Status:** Draft for Tranche 0 review · **Scope:** USDC on Stellar cash-in/out for El Salvador

This document formalizes the architecture shown in [`diagrams/architecture.png`](../diagrams/architecture.png) and the sequence diagrams for [cash-in](../diagrams/cash-in-sequence.png) and [cash-out](../diagrams/cash-out-sequence.png).

## 1. System overview

Muney connects partner applications (wallets, fintechs, money transmitters) with a network of authorized cash merchants. USDC on Stellar is the settlement asset between partner wallets, Muney, and the merchant network. Partners integrate one API/SDK; Stellar powers settlement underneath.

Components, by trust boundary:

### Partner-facing (public surface)
- **User SDK** — used inside partner apps: create cash-in/out requests, quote fees/rates, locate merchants, track order status, receive completion. Never holds keys; talks only to the Muney API Gateway.
- **Merchant SDK** — used by authorized merchants: receive assigned orders, verify QR / one-time codes, confirm cash received/delivered, view commissions, limits, history, and reconciliation. Never holds keys.
- **Partner API** — REST, authenticated per partner, exposing `stellar_usdc` as a selectable settlement rail. Webhook events (signed) for every lifecycle transition.

### Muney core (private)
- **API Gateway** — auth, rate limits, validation, idempotency.
- **Orchestration Engine** — owns the transaction state machine (see [transaction-state-machine.md](transaction-state-machine.md)); routes each order through compliance, funding, merchant assignment, and settlement.
- **Merchant Routing Engine** — selects the best merchant for an order by distance, cash liquidity, limits, historical completion, fees, risk, and operating hours.
- **Compliance Engine** — KYC/KYB/KYT, sanctions screening, risk scoring; can move any order to `pending_compliance_review` / `manual_review`.
- **Quote & Fee Engine** — rates, fees, expiry, final amounts.
- **Ledger & Reconciliation** — internal double-entry records, balances, cutoffs, exports; reconciles the internal ledger against on-chain Stellar history.
- **Webhooks & Notifications** — signed partner events, merchant notifications.

### Stellar integration layer (this repository)
- **Stellar USDC Settlement Adapter** (`stellar-usdc-adapter/`) — the only component that speaks Horizon:
  - deposit detection (streamed payments with persisted cursor, memo-based order correlation),
  - payment transaction construction (never signing),
  - submission and confirmation monitoring (~5s finality),
  - typed lifecycle events consumed by the Orchestration Engine,
  - idempotency and bounded retries.
- **Secure Signing Service** (private, minimal) — the only component with key access. Internal-only (no public route), keys in cloud secret manager, one operation: sign a prepared transaction envelope for an allowlisted source account, with a full audit log. **Keys never exist in the adapter, the SDKs, or partner/merchant surfaces.**
- **Anchor Platform** — Stellar-standard on/off-ramp interface: SEP-10 authentication and SEP-24 interactive deposit/withdrawal, mapped onto Muney's order lifecycle and compliance workflow. SEP-31 is added where a partner needs programmatic cross-border receive.
- **Stellar Disbursement Platform (SDP)** — bulk payout / remittance disbursement flows for partners sending many payouts; recipients and payout status mapped back into Muney's ledger and webhooks.

## 2. Settlement model

- **Asset:** USDC (Circle-issued) on Stellar. Trustlines are established for every Muney-controlled account at provisioning time.
- **Accounts:** Muney operates segregated settlement accounts (per environment; per-partner sub-accounting handled in the internal ledger, with memo/muxed-account correlation on-chain).
- **Correlation:** every expected on-chain movement carries an order reference (transaction memo); the adapter refuses to match unmemoed or ambiguous deposits and flags them `manual_review`.
- **Finality:** an order's digital leg advances on Stellar confirmation (`stellar_transaction_confirmed`); the physical cash leg advances only on explicit merchant + user confirmations.
- **Reconciliation:** the ledger reconciles three views — partner-facing order records, internal double-entry ledger, and on-chain Stellar history — and exports discrepancies for finance/compliance.

## 3. Flows

### Cash-out (USDC → local cash)
1. User selects cash-out in a partner app (User SDK) → quote + nearby merchants.
2. Muney validates partner, user, amount, limits; KYC/KYT screening.
3. Merchant Routing Engine finds a merchant with sufficient cash; order returned with fees, expiration window, merchant location, QR/one-time code.
4. Partner wallet sends USDC on Stellar to Muney's settlement account (via the adapter or SEP-24 withdrawal through Anchor Platform).
5. Adapter detects and validates the incoming transaction (hash, amount, memo) → order becomes `ready_for_cash`.
6. User visits merchant; merchant verifies QR/code and user identity (Merchant SDK) → hands cash → both sides confirm.
7. Ledger updates, merchant account credited with fee, partner receives signed webhook, reconciliation record written.

**Exceptions:** no Stellar payment before expiration → order expires + refund path; merchant lacks cash → reassign to different merchant; compliance flag → `pending_manual_review`; user no-show → timeout expiry; cash dispute → manual resolution queue.

### Cash-in (local cash → USDC)
1. User selects cash-in; Muney validates limits and finds a cash-in-capable merchant; order + QR/code returned (10-minute handoff window).
2. User hands cash to merchant; merchant scans QR, verifies details, confirms cash received; user confirms handoff.
3. Final compliance checks (KYT, sanctions, risk scoring) → approval.
4. Orchestration instructs the adapter to create the USDC settlement transaction; signing service signs; adapter submits and confirms on-chain (~5s).
5. Partner wallet is credited; ledger/reconciliation updated; merchant account credited with fee; signed status webhook to partner.

**Exceptions:** merchant confirmation mismatch → manual review; Stellar submission failure → bounded retry then manual resolution; duplicate confirmation → idempotency guard prevents double credit; incorrect amount → dispute resolution; refund scenario → reverse flow returning cash to user.

## 4. Security model

- **Key isolation:** only the Secure Signing Service holds keys (cloud secret manager, no public network path, source-account allowlist, per-request audit log). Compromise of any SDK, the adapter, or a partner integration cannot move funds.
- **Idempotency:** partner-supplied idempotency keys on order creation; adapter-level idempotency on submission; deposit matching is exactly-once via persisted cursor + unique (tx hash, operation id) constraints.
- **Webhook integrity:** all partner events are signed; replay-protected.
- **Least privilege:** merchants see only their assigned orders; partners see only their own traffic; internal admin actions are role-gated and audited.
- **Compliance events:** every state transition that touches compliance (screening results, manual review outcomes, limits) is recorded against the order for auditability.

## 5. Environments

| Environment | Stellar network | Purpose |
|---|---|---|
| Sandbox | Testnet | Partner integration + pilot simulations; SCF-verifiable transactions |
| Stage | Testnet | Muney internal end-to-end testing |
| Production | Mainnet (pubnet) | Live rail (Tranche 3) |

Testnet setup is scripted in [`scripts/`](../scripts/).

## 6. What is open source vs private

**Open source (this repo):** the settlement adapter, Anchor Platform and SDP integration examples, SDK scaffolds and schemas, webhook payload schemas, state machine specification, testnet scripts, documentation.

**Private:** orchestration engine, merchant routing / liquidity intelligence, risk scoring, compliance rules, internal admin tooling, partner-specific integrations, signing service deployment.
