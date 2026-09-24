Source: https://gitlab.com/holamuney/muney-stellar-integration/-/blob/main/docs/partner-cookbook.md

# Muney Partner Integration Cookbook

How wallets, fintechs, and money transmitters integrate Muney to move **USDC on Stellar** into real-world value — a partner-side payout, a user's cash withdrawal, a bulk disbursement — without building liquidity, a merchant network, or on/off-ramp infrastructure.

This is the practical, copy-paste companion to the [architecture](architecture.md). Everything here runs against the **sandbox** (Stellar testnet); mainnet differs only in base URLs and network.

- **Sandbox API:** `https://devapi.muney.cc`
- **Sandbox Anchor (SEP-24):** `https://devanchor.muney.cc`
- **Sandbox SDP (bulk disbursement):** `https://devsdp.muney.cc`

---

## 0. Which integration do you want?

| You want to… | Use | Section |
|---|---|---|
| Pay a Stellar address USDC and track it | **stellar_usdc rail** via `@muney/user-sdk` | [§2](#2-usdc-on-stellar-payouts) |
| Let your user withdraw USDC as **cash** at a merchant | **Cash-out** via `@muney/user-sdk` | [§3](#3-cash-in--cash-out-merchant-network) |
| Let your user deposit **cash** and receive USDC | **Cash-in** via `@muney/user-sdk` | [§3](#3-cash-in--cash-out-merchant-network) |
| Offer cash-out from **your existing Stellar wallet** with zero Muney API | **Anchor Platform SEP-24** | [§4](#4-sep-24-anchor-platform-standard-wallets) |
| Send **many** payouts at once (payroll, remittance batch) | **Stellar Disbursement Platform** | [§5](#5-bulk-disbursements-sdp) |
| Operate the last-mile cash desk | **Merchant SDK** | [§6](#6-merchant-integration) |

You don't need all of them. Most partners start with §2 or §3.

---

## 1. Setup

### Install

```bash
# routes @muney:* to the GitLab package registry (see the repo .npmrc)
npm install @muney/user-sdk        # partner-facing client
npm install @muney/merchant-sdk    # only if you operate cash merchants
```

### Credentials

Muney issues you a **partner token** (an OrderCreator JWT identifying your `source`) and a **webhook secret**. Keep both server-side.

```ts
import { MuneyUserClient } from '@muney/user-sdk';

const muney = new MuneyUserClient({
  baseUrl: 'https://devapi.muney.cc',   // sandbox
  token: process.env.MUNEY_PARTNER_TOKEN,
});
```

### The mental model

Every Muney order — payout, cash-out, cash-in — is a **state machine** with a signed webhook on every transition. You create an order, then react to webhooks; you never poll in a loop. States and reason codes: [transaction-state-machine.md](transaction-state-machine.md).

---

## 2. USDC-on-Stellar payouts

The simplest rail: pay any Stellar account USDC. Muney signs and submits, monitors confirmation, and webhooks you the lifecycle.

```ts
const order = await muney.createStellarPayout({
  destination: 'GABC...XYZ',       // recipient Stellar account
  amount: '25.50',                 // USDC
  idempotencyKey: 'your-unique-id', // replay-safe: same key → same order, never double-pays
});
// order.state === 'stellar_transaction_submitted', order.tx_hash set

const status = await muney.getStellarOrder(order.order_ref);
// status.history is the full append-only lifecycle
```

Lifecycle you'll see over webhooks:
`created → pending_compliance_review → stellar_transaction_submitted → stellar_transaction_confirmed → payout_routed → payout_completed`

**Idempotency is a hard guarantee.** Retrying `createStellarPayout` with the same `idempotencyKey` returns the existing order and never issues a second on-chain payment — safe to retry on any network error.

---

## 3. Cash-in / cash-out (merchant network)

Turn USDC into physical cash (and back) through Muney's authorized merchants. Your app is the User SDK integrator; Muney assigns a merchant, issues a one-time code, and settles USDC underneath.

### Cash-out (USDC → cash)

```ts
const co = await muney.createCashOut({
  amount: '20',
  userRef: 'your-user-123',
  idempotencyKey: 'co-unique-id',
});
// co.funding tells you where to send the USDC funding leg:
//   { destination, memo, asset: 'USDC', amount }  ← send with this memo
// co.merchant is where your user collects cash
```

1. Send the USDC funding leg to `co.funding.destination` **with `co.funding.memo`** (this is how Muney correlates it).
2. Muney detects funding → order reaches `ready_for_cash` and a one-time code is issued (delivered to you by webhook `cash.order.ready_for_cash`).
3. Show your user the code + `co.merchant` location. They present the code; the merchant hands over cash.
4. Webhook `cash.order.state_changed → payout_completed`.

### Cash-in (cash → USDC)

```ts
const ci = await muney.createCashIn({
  amount: '15',
  destination: 'GABC...XYZ',       // your user's Stellar account for the USDC
  userRef: 'your-user-123',
  idempotencyKey: 'ci-unique-id',
});
// ci.handoff.code  ← show this to your user ONCE
// ci.merchant       ← where they hand over cash
```

Your user gives the merchant cash and shows `ci.handoff.code`. On merchant confirmation, Muney pays USDC to `destination` and webhooks `payout_completed`.

The **physical states** (`merchant_assigned → qr_verified → cash_delivered / merchant_cash_confirmed → merchant_settled`) are visible in every order's history.

---

## 4. SEP-24 (Anchor Platform, standard wallets)

If you already speak Stellar SEP-24 (e.g. a wallet using the standard interactive flow), you can offer **USDC-withdraw-to-cash** with **zero Muney API code** — just point your wallet at Muney's anchor. Muney bridges the withdrawal onto the merchant cash network automatically.

- **Home domain / TOML:** `https://devanchor.muney.cc/.well-known/stellar.toml`
- **Auth:** SEP-10 against `https://devanchor.muney.cc/auth`
- **Withdraw:** SEP-24 `POST /sep24/transactions/withdraw/interactive` (asset `USDC`)

The wallet then: authenticates (SEP-10), starts a withdrawal, opens the returned interactive URL (Muney's bridge), sends USDC on-chain with the given id memo, and the transaction reaches `completed` once the user collects cash at a merchant. Full flow and the moving parts: [anchor-sep24-bridge.md](anchor-sep24-bridge.md).

Use this when you want the standard-wallet UX; use §3 when you want programmatic control.

---

## 5. Bulk disbursements (SDP)

For many payouts at once — payroll, a remittance batch, worker payouts — Muney runs the **Stellar Disbursement Platform**. You upload a CSV of recipients; SDP pays each on Stellar and tracks status.

- **Dashboard:** `https://devsdp.muney.cc` (create disbursements, upload CSV, monitor)
- **API host:** `https://devsdp-api.muney.cc`

Two receiver models:
- **Known wallet address** — recipients' Stellar addresses are in the CSV; no receiver registration needed. CSV columns: `id,amount,paymentID,walletAddress,email`, disbursement type `EMAIL_AND_WALLET_ADDRESS`.
- **Registered receiver** — recipients register a wallet via SEP-24 (for when you only have phone/email).

Operational recipe (auth, tenant, CSV, funding the distribution account): [sdp/README.md](../sdp/README.md). Verified E2E: a disbursement paid on-chain and the receiver's balance credited.

---

## 6. Merchant integration

Only if you **operate** cash desks (you're the last-mile liquidity). Merchants use `@muney/merchant-sdk`.

```ts
import { MuneyMerchantClient } from '@muney/merchant-sdk';

const desk = new MuneyMerchantClient({
  baseUrl: 'https://devapi.muney.cc',
  token: process.env.MUNEY_MERCHANT_TOKEN,   // issued at merchant onboarding
});

const orders = await desk.listAssignedOrders();       // what's waiting for cash
await desk.verifyHandoffCode(orderRef, codeFromUser);  // user presents the code
await desk.confirmCash(orderRef);                      // cash exchanged → settlement proceeds
```

Codes are hashed at rest and reach the user only through the partner app / SEP-24 flow — the merchant sees a code only when the user presents it.

---

## 7. Webhooks (required for every integration)

Muney signs every event: `signature = HMAC-SHA256(timestamp + rawBody)`, hex, in `x-signature`; timestamp in `x-timestamp`. Verify before trusting.

```ts
import { verifyWebhook } from '@muney/user-sdk';

app.post('/muney-webhook', express.raw({ type: '*/*' }), (req, res) => {
  const ok = verifyWebhook({
    rawBody: req.body.toString('utf8'),        // the RAW body, pre-JSON-parse
    timestamp: req.get('x-timestamp'),
    signature: req.get('x-signature'),
    secret: process.env.MUNEY_WEBHOOK_SECRET,
  });
  if (!ok) return res.sendStatus(401);
  const event = JSON.parse(req.body.toString('utf8'));
  // event.type: order.state_changed | order.completed | order.failed |
  //             cash.order.ready_for_cash | cash.order.state_changed | ...
  res.sendStatus(200);   // ack fast; make handlers idempotent (deduplicate on event id)
});
```

Rules: verify the signature, reject stale timestamps (replay window), **deduplicate on the event id** (deliveries may retry), and never assume ordering — reconcile against `previous_state`/`state`. Payload shapes: [webhooks.md](webhooks.md) and [examples/](../examples/).

---

## 8. Going to production

- Base URLs drop the `dev` prefix; the network becomes Stellar **pubnet** and the asset is mainnet USDC.
- Your sandbox `idempotencyKey` discipline and webhook verification carry over unchanged.
- Compliance (KYC/KYB/KYT) that auto-passes in sandbox becomes real; some orders will legitimately sit in `pending_compliance_review` or `manual_review`.
- Talk to Muney for your production partner token, webhook secret, and merchant/liquidity coverage in your corridor.

---

## Reference

- [architecture.md](architecture.md) — how the pieces fit
- [transaction-state-machine.md](transaction-state-machine.md) — every state + reason code
- [webhooks.md](webhooks.md) — event catalog + signing
- [anchor-sep24-bridge.md](anchor-sep24-bridge.md) — SEP-24 ↔ cash internals
- [sdp/README.md](../sdp/README.md) · [anchor-platform/README.md](../anchor-platform/README.md) — deployment guides
