Source: https://github.com/mpprouter/mpp-spec/blob/main/README.md

# Technical Doc - ROZO: Intent-Based Pay for AI Services via Stellar MPP & x402


## 1. System Overview

```text
                       +---------------------------------------------+
                       |        Buyer side: human or agent            |
                       |  OpenClaw skills / agent frameworks / CLI    |
                       |          -> Buyer SDK (BSD/MIT)           |
                       +----------------------+----------------------+
                                              |
                                              | 1. GET /service -> HTTP 402 challenge
                                              | 2. deterministic parse -> sign -> pay
                                              v
        +-------------------------------------------------------------+
        |                       Stellar mainnet                        |
        |  x402 path: Soroban auth-entry -> facilitator verify/settle  |
        |  MPP path: classic USDC payment + nonce-memo binding         |
        +----------------------+------------------------+-------------+
                               |                        |
                               v                        v
        +-----------------------------+   +----------------------------+
        | Intent providers / sellers   |   | Quality metrics pipeline   |
        | - MPP Router (ROZO, first)   |   | - public Dune dashboard    |
        | - any third-party provider   |   | - per-payer history API    |
        |   with own key and payments  |   | - provider ranking         |
        +-----------------------------+   +----------------------------+
```

Component-to-deliverable mapping:

| Component | Deliverable | Tranche |
|---|---|---|
| Open spec: MPP dialect, catalog format, receipt/refund rules, provider registration | spec doc + SEP / ecosystem review submission | T1 |
| Buyer SDK | Apache/MIT open source, including no-ROZO acceptance test | T1 |
| Seller / provider library | open-source server library + onboarding guide | T1 |
| MPP Router, first operator | live today at `apiserver.mpprouter.dev`; catalog declares `stellar.x402` + `stellar.mpp` on pubnet; implements the §3.4 channel offer and channel registration on pubnet since 2026-09-15 | existing |
| Quality metrics + Dune dashboard | public dashboard + per-payer history API | T1 basic / T2 full |
| Verified merchant network | top 10 services verified payable, each with reproducible paid call on Dune | T1 |
| Non-ROZO intent providers | T2 >= 1 as payout gate, T3 >= 2 | T2/T3 |

Two capabilities distinguish the MPP extension from exact x402: **output-metered pricing** (charge after the result exists, §2.3) and **trusted-buyer fast settlement** (< 5s confirmation for returning buyers, §2.4). Both come from the same primitive: the session.

## 2. Two Payment Paths

§2.1 is the unmodified official stack, summarized for completeness only. ROZO's original contributions are §2.2–§2.4.

### 2.1 x402 Path: Official Fixed-Price Calls

This path follows the official Stellar x402 stack. ROZO does not reinvent it.

1. Service returns `HTTP 402` with a payment challenge.
2. Buyer SDK parses deterministically.
3. Buyer signs a **Soroban auth entry**, Stellar's equivalent pattern for authorized transfer.
4. Facilitator, Coinbase or OpenZeppelin Relayer, verifies and settles. It can sponsor fees and remains non-custodial.
5. Service verifies settlement and delivers.
6. Seller-signed receipt returns.
7. Metrics pipeline records the event.

Spending limits are enforced on-chain by OpenZeppelin audited smart accounts.

### 2.2 MPP / Session Path: ROZO Extension

The extension has two purposes:

1. **Compatibility:** today only a limited set of wallets can sign auth entries. Any wallet that can send USDC with a memo can use the MPP path.
2. **Variable-cost pricing:** many AI/data calls have final cost only after output exists.

Single-payment MPP exact flow:

1. Provider creates challenge: `{ payTo, amount, asset(USDC:issuer), network, nonce, expiry, receiptWindow }`.
2. Buyer sends a classic USDC payment with the **challenge nonce bound in the tx memo**. Long nonces use hash binding.
3. Provider verifies through Horizon/RPC: exact amount, asset, destination, memo, and finality all match.
4. Provider delivers, returns seller-signed receipt, and metrics record the event.

Explicit edge handling:

- **Replay:** nonce is single-use. Provider marks it consumed on first valid payment. Duplicate payment to the same nonce enters refund flow.
- **Amount mismatch:** exact match is required. Overpayment or underpayment is not fulfilled and enters refund flow.
- **Expiry:** challenge includes expiry. Late payment is refunded according to refund flow.

### 2.3 MPP Sessions for Variable-Cost Calls

**Why exact x402 cannot price these calls.** x402 charges at order creation: the amount is fixed inside the 402 challenge, before the request executes. For AI calls, true cost = input tokens (known at order time) + output tokens (known only after execution — and every major LLM API prices input and output separately). A pay-before-execution protocol therefore has only two options, both wrong: quote the maximum possible output (systematic overcharge) or quote a flat price (mispriced in both directions). This is not an implementation gap — it is the shape of the protocol. Exact x402 remains the right tool for fixed-price calls; MPP sessions exist precisely for the calls it cannot express: open a bounded session, meter actual output, settle actual usage.

```text
open session  ->  meter usage  ->  settle actual  ->  receipt
(budget max)      (actual use)      (paid <= max)      (quoted max + actual)
```

1. Buyer opens a session: `{ sessionId, budgetMax, asset, expiry, settlementPolicy }`.
2. Provider meters actual usage: output tokens, duration, or data size.
3. Settlement amount is <= `budgetMax`.
4. Receipt records both `quotedMax` and `actualAmount`, plus usage units.

Trust model, stated explicitly in the spec:

- MPP delivery and payment are **not atomically bound**. Refund is provider policy plus spec rule, not protocol-enforced.
- If a custodial wallet rewrites the memo, MPP path does not apply.
- The trust model is weaker than official x402 with facilitator + auth entry. The tradeoff is broader wallet/provider compatibility.

### 2.4 Trusted-Buyer Fast Settlement (returning buyers, < 5s)

For a first-time buyer, the MPP flow waits for on-chain finality (~5s) plus provider verification before delivery. For a **returning buyer with verifiable on-chain payment history**, the provider can decouple confirmation from settlement:

1. **Instant confirmation.** Based on the buyer's on-chain history, the provider extends a small pre-approved allowance (bounded per call and per buyer). The call is confirmed and delivered in **under 5 seconds** — before the payment transaction reaches finality.
2. **Deferred settlement.** The settling transaction lands on-chain seconds behind. The chain remains the source of truth: the receipt binds delivery to the settling tx hash once it lands.
3. **Batched netting (session mode).** Within a session, N calls settle as a single on-chain transaction. Per-call marginal confirmation is a metered ledger update (milliseconds); the session settles `actualAmount <= budgetMax` in one payment.

Trust model: the allowance is **operator risk, not buyer risk**. If settlement fails, the provider bears the loss, the allowance is revoked, and the buyer is downgraded back to exact-payment mode. Allowance bounds (per-call cap and per-buyer outstanding cap) are declared at provider registration.

## 3. Spec Field Definitions, v1 Draft

### 3.1 MPP Challenge

| Field | Type | Description |
|---|---|---|
| `payTo` | G-address | provider-owned receiving key |
| `amount` | string | exact amount; in session mode this is `budgetMax` |
| `asset` | string | `USDC:<issuer>` |
| `network` | string | `stellar:pubnet` |
| `nonce` | string | single-use, request-bound; long nonce hash-bound into memo |
| `expiry` | ISO8601 | challenge expiry time |
| `receiptWindow` | seconds | delivery/receipt deadline; timeout = non-delivery |

### 3.2 Seller-Signed Receipt

| Field | Description |
|---|---|
| `txHash` | on-chain transaction hash |
| `nonce` | corresponding challenge |
| `deliveryStatus` | delivered / non-delivery / refunded |
| `quotedMax` / `actualAmount` | both present for session mode; equal for exact mode |
| `units` | billing units, such as tokens / seconds / bytes, if applicable |
| `timestamp` | delivery time |
| `signature` | SEP-10-style signature by the **provider's own Stellar key**; receipt is seller-attested, not ROZO-attested |

Anyone can verify a receipt independently using the provider public key.

### 3.3 Provider Registration

| Field | Description |
|---|---|
| `name` / `endpoint` / `vertical` | service identity and category: AI inference or blockchain/data |
| `payTo` | provider receiving address; provider keeps its key; delegated signing / scoped operational keys supported |
| `schemes` | supported dialects: `stellar.x402` / `stellar.mpp` |
| `pricing` | fixed / per-unit / session |
| `refundPolicy` | provider-specific declaration on top of default spec rules |

Registration is **self-service and pluggable**: a provider onboards by pointing its own domain/endpoint at the spec — no permission from ROZO required — and its catalog entry goes live automatically. The public catalog addresses the agent resource discovery gap, and every entry enters the Dune indexing set and the open provider scorecard (§6.2).

### 3.4 Channel Offer and Channel Registration

A router serving many agents cannot be configured with a channel before the
agent opens it: `stellar.channel({ channel, commitmentKey })` names ONE
deployed channel. Two additions close that loop — one field group on the 402,
one endpoint — and the per-call flow is unchanged MPP.

**Channel offer** — an additional entry in the 402's `accepts[]`:

| Field | Description |
|---|---|
| `scheme` | `channel` |
| `network` | `stellar:pubnet` |
| `asset` | the SAC the channel holds, e.g. the USDC SAC `CCW67TSZ…` |
| `payTo` | the recipient (`to`) the channel is constructed with |
| `extra.factory` | the router's channel factory, e.g. `CCR2HE6C…` on pubnet |
| `extra.wasmHash` | the code hash the factory deploys, e.g. `d6717aa8…` |
| `extra.refundWaitingPeriodMinLedgers` | `100` for this Router (§3.4.2) |
| `extra.register` | where the agent announces a freshly opened channel |
| `extra.minDeposit` | human-readable amount in `asset` |

`extra.wasmHash` is not a trust anchor on its own. The factory already refuses
to deploy any hash but the one it stores, so against an honest factory the
field is redundant; and a hostile 402 can advertise a hostile factory *and* a
matching hash. Its use is for a client holding an independent pin — one read
from the factory's own `WasmHash` storage entry, say — to compare against.
Note also that the factory pins the **code, not the parameters**: `token`,
`from`, `to` and `commitment_key` are arguments to `open`, so a channel from
the right code can still name the wrong recipient or asset. That is why
registration reads the getters rather than trusting provenance.

**Channel registration** — `POST {extra.register}`:

```json
{ "channel": "C…", "commitmentKey": "G…", "salt": "<hex>", "from": "G…" }
```

`salt` and `from` are required: without them the router cannot recompute the
deterministic deploy address, and cannot authenticate the request.

Before answering 200 the router verifies on-chain:

1. the address is what `factory` deploys for that `salt`;
2. the instance's code hash equals the factory's stored `WasmHash` — code and provenance checked separately;
3. `to()` == `payTo`, `token()` == `asset`, `commitment_key()` == `commitmentKey`, `refund_waiting_period()` as required (§3.4.2);
4. `balance()` >= `minDeposit`;
5. the channel's close state and, if closing, the effective ledger — a channel already counting down offers less than the full window;
6. the request is authenticated as `from`. Without this anyone can bind a channel whose identifiers are public, grief the real agent, and leave its deposit locked for a window.

4xx with a reason on any failed check; the agent falls back to the `exact`
offer. The registered channel then becomes a row in the MPP store rather than
a constant.

**Implementation status (2026-09-15, updated the same day).** §3.4 is
implemented by this Router on the metered channel endpoints
(`/v1/playground/channel/{chat,blend-activity,tx-decode}`), verified on
pubnet against factory `CCR2HE6C…` (rozo-mpprouter #163, #164):

1. **Channel offer.** Every 402 those endpoints issue (`channel_not_registered`,
   `insufficient_channel_balance`, and the voucher challenge) carries an
   x402 envelope `{ x402Version: 2, resource, accepts: [ { scheme: "channel",
   network, asset, payTo, amount, extra: { factory, wasmHash,
   refundWaitingPeriodMinLedgers: 100, register, minDeposit } } ] }` in the
   JSON **body**, next to the mppx `WWW-Authenticate` challenge. `amount` is
   this call's voucher increment. The same object is `channel.offer` in
   `GET /v1/playground/config`. The offer is not placed in the x402
   `Payment-Required` header: mppx 0.7.0 clients decode that whole header
   against an `exact`-and-EVM-only schema and discard the mppx challenge when
   any other entry is present, which would break every existing channel
   client on its first probe.
2. **Registration** takes the spec body `{ channel, commitmentKey, salt, from,
   signature }` and recomputes the factory's deterministic address
   (`sha256(xdr(DeploymentSaltPreimage(from, salt)))` as the deployer salt,
   standard contract-id preimage); a mismatch is `400 address_mismatch`. The
   legacy playground body is still accepted for the frontend until it
   migrates.
3. **Authentication as `from`.** `signature` is `from`'s ed25519 signature
   over `mpprouter.channel-register.v1\n<channel>\n<commitmentKey>\n<salt hex>\n<from>`,
   as a raw signature or a SEP-53 signed message; missing or wrong is `401`.
   Re-registration of the same tuple stays idempotent.

Not covered: the paid proxy (`/v1/services/*`) still advertises only `exact`.
Its channel branch reads an operator-managed registry, so a self-registered
channel would not be honored there and the offer is deliberately not made.

#### 3.4.1 Recipient obligation before delivering value

Per call the agent sends the usual voucher `{ action: "voucher", amount,
signature }` over the cumulative amount. Before delivering value the recipient
MUST do both:

- verify the ed25519 signature **strictly** (`verify_strict`; the chain refuses small-order keys), **and**
- check the commitment's cumulative amount against live `balance()` and `withdrawn()`.

These are not alternatives, and the second is not about `settle` reverting.
`withdraw` pays `owed.min(balance)`: a commitment for more than the channel
holds does **not** fail — `settle` succeeds and silently pays short. A
signature that verifies perfectly can still leave the recipient underpaid,
which is why the channel contract's own recipient expectations require
verifying that each commitment's amount is less than the channel's balance.

Note for implementers: `settle` carries no close check. It remains callable
throughout the refund window and after the close is effective, up until
`refund` drains the balance.

#### 3.4.2 Refund waiting period

`close_start` begins a wait of `refund_waiting_period` ledgers before the
funder may `refund`; that window is the recipient's only chance to submit the
latest voucher. Measured over 199 consecutive pubnet ledgers, mean close time
is **5.60 s**, so 100 ledgers is about **9.3 minutes**.

This Router requires **exactly 100** and enforces it at registration the same
way it enforces the WASM hash, settles from a 2-minute cron that fences a
channel on the first tick observing `close_start` and retries every tick until
submission lands (about 4.6 ticks inside the window), and refuses to pay
upstream on a channel that has entered `close_start`, bounding exposure to
vouchers already served. Its design floor is 60 ledgers — two cron ticks plus
settle latency. A spec needing a single number should use 100.

A recipient adopting a different value should derive it the same way: the
window must clear its own close-monitoring interval plus settle latency plus
retries, and the channel contract places that verification on the recipient
at channel creation.


## 4. Pricing Model: Exact vs Session

| | Exact x402, official | MPP session, extension |
|---|---|---|
| Best for | fixed-price calls where price is known | variable-cost calls where cost depends on output |
| Typical scenarios | fixed-price APIs, per-call queries | LLM output tokens, browser duration, data job return size |
| Charge timing | at order creation — amount fixed in the 402 challenge, before output exists | after execution — session opened with `budgetMax`, actual output metered, settle actual |
| Buyer cost | provider quotes maximum possible output, causing systematic overpayment | paid amount = actual usage |
| Example | fixed $0.05 endpoint | LLM quoted max $0.20 for 4K output tokens, actual output 1.1K, settles $0.06 |

Positioning: sessions are an **extension** to official x402, not a replacement. Fixed-price endpoints keep using exact x402.

## 5. Security Model

First principle: **no LLM constructs or modifies a payment transaction.**

The LLM may express intent. Amount, destination, asset, memo, nonce, expiry, and receipt window are built deterministically by the SDK from challenge fields. The key holder signs. In agent contexts, the model decides whether to buy, never what the payment contains.

| Control | Mechanism | Enforcement Layer |
|---|---|---|
| Agent spend cap | default agent deployment uses a sub-account holding only the daily budget, default $5/day | on-chain balance |
| x402 path spend cap | OpenZeppelin audited smart accounts with spending limits / scoped permissions | on-chain contract |
| Human confirmation | above $10 (default) requires human approval; human is policy-setter/auditor | SDK policy |
| Allowlist | service / provider allowlist | SDK policy |
| Idempotency | client-side idempotency key | SDK |
| Replay prevention | single-use nonce + expiry | provider |
| Refund-on-non-delivery | objective non-delivery: HTTP 5xx, timeout, or empty response within receipt window; refund to paying key; operator bears loss | spec + provider |
| Trusted-buyer allowance | bounded per-call cap + per-buyer outstanding cap; revoked on settlement failure; operator bears loss (§2.4) | provider policy + spec |
| Sponsored fee abuse model | only keys with >= $1 USDC; per-key daily cap; global sponsorship budget and circuit breaker; abusive keys delisted | operator |
| Order state machine | created -> paid -> delivered / non-delivery -> refunded | spec |

Content refusal / policy refusal counts as delivered by default unless the provider declares a different policy during registration.

## 6. Quality Metrics and Verification

### 6.1 Indexing Methodology

- **MPP path:** index USDC payments to spec-registered seller addresses where memo matches the spec nonce format. Registration is open and free, so ROZO and non-ROZO sellers are indexed equally.
- **x402 path:** best-effort indexing of transactions settled through publicly identifiable facilitator accounts (source accounts + Soroban events). Where this proves unreliable, v1 metrics scope is registered sellers plus submitted receipts — a receipt is first-class evidence on its own (§3.2) and does not depend on Dune.
- **Honesty boundary:** only registered sellers and facilitator-settled traffic are indexed. We do not claim to cover unregistered payments.
- **Integrity:** ROZO-owned and test keys are published and excluded from KPIs. Subsidy- or referral-driven cohorts are labeled. Traffic violating integrity rules can be rejected during tranche review.

### 6.2 Metrics

Headline: **settlement time**.

- First-time buyer: payment / settlement / delivery-state visibility target P95 < 10s.
- Returning buyer (trusted-buyer fast settlement, §2.4): confirmation **< 5s**, with on-chain settlement following seconds behind or netted per session.

Current state: 95% of cross-chain orders into Stellar are around ~20 seconds. These targets do not include upstream model runtime.

Other metrics:

- transaction count
- USDC volume
- unique external payers
- per-service mix
- non-ROZO provider share
- p95 latency
- success rate
- refund rate
- failure type
- cost per comparable task

Per-payer history is provided through an **API / query capability**, not a heavy dashboard: payer address -> paid calls, tx hash, service, quoted vs actual, delivery/refund state, receipt.

**Open provider scorecard.** Every catalog provider gets a public scorecard, published alongside its catalog entry, so any buyer — human or agent — can compare and rank providers before paying:

- **Speed:** p50/p95 end-to-end latency per service.
- **Price:** declared unit price (per call / per token / per second) and observed cost per comparable task.
- **Cache:** whether cached pricing is offered, observed cache hit rate, and the cached-call discount.
- **Reliability:** success rate, refund rate, uptime.

Scorecard inputs come from on-chain settlement data plus open probes against the provider's declared endpoint. The methodology is public: anyone can re-measure and rebuild the ranking independently.

### 6.3 P95 < 10s Budget, Payment Layer Only

| Stage | Target |
|---|---|
| challenge parse + construct + sign | < 1s |
| native Stellar confirmation | ~5s finality |
| provider verification through Horizon/RPC | < 2s |
| delivery-state / receipt visibility | < 2s |

Cross-chain top-up through Rozo Intents is currently ~20s at p95 and continues to improve. Prefunded fulfillment can make the user experience visible before upstream settlement fully completes.

For returning buyers, trusted-buyer fast settlement (§2.4) removes the on-chain wait from the critical path entirely: confirmation in under 5 seconds, settlement seconds behind or netted per session.

## 7. Reviewer-Reproducible Acceptance Tests

1. **No-ROZO acceptance test, T1:** Buyer SDK completes a paid call against a non-ROZO spec-compliant endpoint with no ROZO server, router, or account in the loop. Execution record is public.
2. **Top 10 verified payable, T1:** each initial service has one reviewer-reproducible paid call, evidenced by a public receipt and, where indexed, the public Dune dashboard.
3. **Receipt independent verification:** any receipt can be verified offline with the provider public key.
4. **Non-ROZO provider, T2/T3 gate:** third party deploys with seller library, holds its own key, generates real external volume, and appears with operator attribution on the dashboard.

The no-ROZO acceptance test can also produce the live-demo artifact for the video if recorded in one clean mainnet take.

## 8. Dependencies and Failure Surfaces

| Dependency | Failure Impact | Mitigation |
|---|---|---|
| Official facilitators, Coinbase / OZ Relayer | x402 path temporarily unavailable | MPP path has no facilitator dependency and remains payable |
| Stripe/Tempo gateways | gateway-routed services may lose payable status | first-party services and OpenRouter path unaffected; provider interface can onboard replacements; spec/SDK/metrics are gateway-agnostic |
| MPP Router operated by ROZO | ROZO operator offline | spec + SDK + provider library are open; non-ROZO providers continue serving |
| Dune | dashboard unavailable | indexing rules are public; anyone can rebuild from on-chain data; receipts remain independently verifiable without Dune |

## Secret scanning

Enable the local gitleaks pre-commit hook once per clone: `brew install gitleaks pre-commit && pre-commit install` (config in `.pre-commit-config.yaml`). CI also runs a report-only scan in `.github/workflows/secret-scan.yml`.
