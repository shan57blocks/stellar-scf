Source: https://gitlab.com/holamuney/muney-stellar-integration/-/blob/main/README.md

# Muney × Stellar Integration

**Stablecoin-to-Cash Layer Infrastructure** — SCF #44 Build Award (Integration Track).

Muney is B2B stablecoin payment infrastructure for emerging markets. This repository contains the open-source components of Muney's **USDC-on-Stellar settlement rail**: a production-grade settlement adapter, Anchor Platform and Stellar Disbursement Platform integration examples, SDK scaffolds, schemas, and documentation — built in public as part of the SCF Build Award deliverables.

> Muney's core orchestration engine, liquidity routing, merchant network coordination, risk scoring, and compliance rules remain in private repositories. This boundary is intentional: we contribute reusable Stellar integration components while protecting the operational infrastructure required to run real-world payment flows securely.

## What this integration does

Partners (wallets, fintechs, money transmitters) call Muney's API to create **cash-in / cash-out** orders in El Salvador. USDC on Stellar settles the digital leg; an authorized Muney merchant handles the physical cash leg. Partners never touch Stellar directly — they select `stellar_usdc` as a settlement rail and receive webhooks.

```
USDC on Stellar  ──►  Muney API + orchestration + compliance  ──►  cash-out / cash-in
(settlement)          (state machine, routing, recon)              at authorized merchants
```

## Integrating with Muney

**Start here: [docs/partner-cookbook.md](docs/partner-cookbook.md)** — the practical, copy-paste guide for wallets, fintechs, and money transmitters (payouts, cash-in/out, SEP-24, bulk disbursements, webhooks).

## Repository layout

| Path | Contents |
|---|---|
| `docs/` | Architecture, transaction state machine, webhooks, roadmap by tranche |
| `diagrams/` | Technical architecture + cash-in / cash-out sequence diagrams |
| `stellar-usdc-adapter/` | Open-source Stellar USDC settlement adapter (Node.js) |
| `sdk/user-sdk/` | User SDK — for partner apps offering cash-in/out |
| `sdk/merchant-sdk/` | Merchant SDK — for authorized cash merchants |
| `examples/` | API request/response and webhook payload examples |
| `scripts/` | Testnet setup and demo scripts |

## Stellar building blocks used

- **USDC on Stellar** — core settlement asset.
- **Stellar Anchor Platform** — SEP-10 auth, SEP-24 deposit/withdrawal (SEP-31 where applicable).
- **Stellar Disbursement Platform (SDP)** — bulk payout / remittance disbursement flows.

## Status

Delivery is organized by SCF tranches — see [docs/roadmap-by-tranche.md](docs/roadmap-by-tranche.md).

| Tranche | Scope | Status |
|---|---|---|
| 0 | Architecture, testnet env, this repository | 🚧 in progress |
| 1 | Testnet USDC settlement adapter + `stellar_usdc` rail | ⏳ |
| 2 | Anchor Platform + SDP + User/Merchant SDKs + sandbox | ⏳ |
| 3 | Mainnet launch + El Salvador merchant pilot | ⏳ |

## License

Apache-2.0 (see `LICENSE`).
