Source: https://gitlab.com/holamuney/muney-stellar-integration/-/blob/main/docs/soroban-business-rules.md

# Business Rules in Smart Contracts (Soroban) — Considerations

Evaluation of moving Muney business rules (fees, commissions, settlement conditions, escrow) into Soroban contracts, framed as **transparency vs cost**, plus the constraints that actually decide it.

## Cost reality first: "gas" is not the problem on Stellar

Soroban does not use Ethereum-style gas auctions. Fees are **multidimensional resource fees** (CPU instructions, ledger reads/writes, bandwidth) plus **state rent** for persistent storage, denominated in XLM. In practice a contract invocation costs **fractions of a US cent** — orders of magnitude below the per-transaction economics of cash-in/out (where merchant commissions are measured in percent, not micro-cents). Two real cost items to watch:

- **State rent**: persistent contract entries have a TTL and must be periodically extended (rent), an operational chore rather than a real expense at our scale.
- **Resource limits**: per-transaction CPU/IO caps constrain how much logic one invocation can run — contracts must stay small and focused.

**Conclusion on cost: fees are a non-factor for adopting or rejecting Soroban.** The decision rests on the other axis.

## Transparency: it cuts both ways

**Pros**
- **Verifiable settlement guarantees.** Partners (and SCF reviewers) can independently verify escrow and release conditions: "your USDC is locked per-order and released only on completion, refunded on timeout" becomes provable, not promised. Strong B2B trust signal.
- **Auditability.** On-chain rule execution produces an immutable audit trail aligned with our compliance narrative.
- **Ecosystem alignment.** The SCF submission already lists Soroban under future/optional phases ("transaction auditability, programmable settlement") — building it later is a natural follow-on grant story.

**Cons**
- **Competitors read your rules.** On-chain fee splits, commission tiers, and routing parameters expose exactly the "liquidity intelligence" that differentiates Muney from MoneyGram. Publishing the pricing brain forfeits the moat.
- **Business rules change faster than contracts should.** Fees, limits, and jurisdisction-specific rules change per partner/market/week. Every upgrade path (admin keys, upgradeable contracts) reintroduces a trusted party — which quietly cancels much of the transparency benefit.
- **Bugs are catastrophic in money contracts.** Serious audits are slow and expensive; that is incompatible with the current build timeline.

## The decisive constraint: the cash leg is off-chain

A contract can only enforce facts it can see. **"Merchant handed cash to the user" is not an on-chain fact** — it enters the chain as an attestation signed by Muney (or the merchant). So a contract that "enforces" cash-out completion is really enforcing *Muney's signature*, i.e. the trust model is unchanged; only ceremony was added. The same applies to compliance: KYT, sanctions screening, and manual review are inherently off-chain and must be able to interrupt any flow.

## Recommendation

1. **Do not put pricing, commissions, or routing rules on-chain.** Keep them in the orchestration engine where they iterate daily and stay confidential.
2. **Do consider one narrow contract in a later phase: per-order settlement escrow.**
   - Partner funds are locked per order in a Soroban contract holding USDC (via the Stellar Asset Contract).
   - Released to Muney settlement on a completion attestation; **auto-refunded to the partner after timeout** — the refund path is the part that needs no oracle and is therefore genuinely trustless.
   - Transparent guarantees for partners without exposing any business logic.
3. **Sequence:** nothing in Tranches 0–3 requires Soroban (classic payments + Anchor + SDP cover the award scope). Prototype the escrow contract post-mainnet-launch, as the "programmable settlement" phase already hinted at in the submission.

| Option | Transparency win | Cost | Risk |
|---|---|---|---|
| Pricing/routing rules on-chain | Low (nobody demands it) | Fees negligible; audit + iteration cost high | Exposes the moat; upgrade-key trust paradox |
| Per-order escrow contract | High (provable refunds/locks) | Fees negligible; one focused audit | Contained; oracle needed only for the happy path |
| Status quo (off-chain rules, classic payments) | Baseline (webhooks + on-chain tx evidence) | Zero | None new |
