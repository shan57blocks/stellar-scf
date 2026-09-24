Source: https://gitlab.com/holamuney/muney-stellar-integration/-/blob/main/docs/anchor-sep24-bridge.md

# Anchor Platform SEP-24 ↔ Muney cash-network bridge

A wallet's SEP-24 **USDC withdrawal** is fulfilled as **cash at a Muney merchant**. The Anchor Platform handles the Stellar/wallet protocol; Muney's `external-orders` service hosts the interactive flow and drives the transaction through the Platform API as the physical cash leg progresses.

**Verified end-to-end on the sandbox (2026-07-19):** a real SEP-24 withdrawal reached `completed`, linked to a Muney cash order — the wallet's USDC settled on-chain, a merchant confirmed the cash hand-off, and the Anchor transaction closed.

## Flow

```
wallet ──SEP-24 withdraw──▶ Anchor Platform ──interactive URL──▶ external-orders /orders/anchor/interactive
                                                                        │ validates the interactive JWT
                                                                        │ creates a linked cash_out order (merchant assigned)
                                                                        ▼
                              Platform API ◀── notify_interactive_flow_completed + request_onchain_funds
                                                (destination = settlement account, MEMO_ID)
wallet ──USDC on-chain (id memo)──▶ settlement account
                                                                        │ reconciliation cron (1 min):
                                                                        │   Horizon id-memo match → notify_onchain_funds_received
                                                                        │   → pending_anchor → order ready_for_cash (+ one-time code)
                                                                        ▼
user ──code──▶ merchant ──confirm cash──▶ order payout_completed ──notify_offchain_funds_sent──▶ SEP-24 completed
```

## Anchor Platform config that this requires (all in `anchor-platform/config/anchor-config.yaml`)

- `sep24.interactive_url.base_url: https://devapi.muney.cc/orders/anchor/interactive` — Muney hosts the interactive flow.
- `sep24.deposit_info_generator_type: none` — the business (external-orders) supplies `destination_account` + `memo` in `request_onchain_funds`.
- A fiat asset `iso4217:USD` (withdraw `amount_out` must be non-Stellar). Because SEP-31 is enabled (see below), the fiat asset needs a `sep31.receive` block.
- SEP-6 + SEP-31 enabled — the Platform's `exchangeAmountsCalculator` bean is gated on all of sep6+sep24+sep31, while the SEP-24 controller depends on it. No assets expose sep6/sep31 receive beyond what validation demands.

## Platform API (JSON-RPC) facts learned

- The RPC endpoint is **POST to the platform server root** (`/`), not `/transactions`.
- `request_onchain_funds.memo` must be a **MEMO_ID** (numeric); there is no `memo_type` field.
- The Anchor Platform's payment observer does **not** watch a business-supplied destination account, so Muney detects the withdrawal USDC itself (Horizon, id-memo match) and calls `notify_onchain_funds_received`.

## Muney side (external-orders)

- `controller/anchor.js` — interactive endpoint, Platform-API client, reconciliation cron.
- `controller/cash.js` — `createSep24Withdrawal`, `advanceAnchorOrderToReady`, `failAnchorOrder`.
- `CashOrder` gains `sep24_transaction_id` + `anchor_memo`.
- Env-gated: absent `SEP24_INTERACTIVE_JWT_SECRET` disables the bridge (prod stays dark until mainnet).
