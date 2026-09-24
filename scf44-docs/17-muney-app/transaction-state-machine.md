Source: https://gitlab.com/holamuney/muney-stellar-integration/-/blob/main/docs/transaction-state-machine.md

# Transaction State Machine

B2B partners don't just need a transaction hash — they need operational certainty: where the money is, what state the payout is in, whether compliance review is required, and what happens on failure. The state machine is the contract between Muney, partners, merchants, and reviewers.

It has two layers: **digital settlement states** (the Stellar leg) and **physical cash states** (the merchant leg). Cash-in and cash-out traverse them in different orders.

## States

### Digital settlement
| State | Meaning |
|---|---|
| `created` | Order accepted, quote locked, expiration window started |
| `pending_compliance_review` | Screening (KYC/KYT/sanctions/risk) in progress or flagged |
| `pending_funding` | Waiting for USDC on Stellar to arrive (cash-out) |
| `stellar_transaction_submitted` | Settlement tx signed and submitted to the network |
| `stellar_transaction_confirmed` | On-chain confirmation validated (hash, amount, memo) |
| `payout_routed` | Orchestration selected the payout/settlement path |
| `payout_completed` | Terminal — value delivered, ledger closed |
| `failed` | Terminal — with machine-readable reason code |
| `refunded` | Terminal — value returned to origin |
| `manual_review` | Parked for human decision (any stage) |

### Physical cash (merchant leg)
| State | Meaning |
|---|---|
| `merchant_assigned` | Routing engine matched a merchant with capacity |
| `ready_for_cash` | Digital leg secured; user may visit the merchant |
| `user_arrived` | User present at merchant (optional, SDK-reported) |
| `qr_verified` | Merchant validated the QR / one-time code |
| `cash_delivered` | Merchant handed cash to user (cash-out) |
| `merchant_cash_confirmed` | Merchant confirmed cash received (cash-in) |
| `dispute_opened` | Either side disputed the physical exchange |
| `settlement_pending` | Merchant leg done; merchant settlement queued |
| `merchant_settled` | Merchant liquidity/fees settled — merchant leg terminal |

## Canonical paths

**Cash-out (USDC → cash):**
```
created → pending_compliance_review → merchant_assigned → pending_funding
        → stellar_transaction_confirmed → ready_for_cash
        → qr_verified → cash_delivered → settlement_pending
        → merchant_settled → payout_completed
```

**Cash-in (cash → USDC):**
```
created → merchant_assigned → qr_verified → merchant_cash_confirmed
        → pending_compliance_review → stellar_transaction_submitted
        → stellar_transaction_confirmed → settlement_pending
        → merchant_settled → payout_completed
```

## Rules

1. Transitions are **append-only events** — an order's history is immutable and auditable; current state is a projection.
2. Every transition emits a **signed partner webhook** (see [webhooks.md](webhooks.md)) and, where relevant, a merchant notification.
3. `manual_review` and `dispute_opened` are reachable from any non-terminal state; exit requires a recorded human decision.
4. Expiration timers: order expiry (quote window), cash handoff window (e.g. 10 min), funding timeout — each fires a deterministic transition (`failed` with reason, or refund path).
5. Idempotency: duplicate confirmations (merchant double-tap, replayed webhook, re-detected deposit) must not double-move state or funds — guarded by unique event keys.
6. Reason codes on `failed` / `refunded` / `manual_review` are part of the public API surface (see `error-codes` in the partner docs).

The machine-readable definition lives in [`stellar-usdc-adapter/src/states.ts`](../stellar-usdc-adapter/src/states.ts) — the allowed-transition map there is the source of truth, and the automated test suite asserts every documented path and rejects everything else.
