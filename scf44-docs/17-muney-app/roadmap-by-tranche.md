Source: https://gitlab.com/holamuney/muney-stellar-integration/-/blob/main/docs/roadmap-by-tranche.md

# Roadmap by Tranche — SCF #44 Build Award

Delivery is organized around the four SCF tranches. Every deliverable lands in this repository (code, docs, examples) or is demonstrated with verifiable evidence (testnet/mainnet transaction hashes, recorded demos).

## Tranche 0 — Award acceptance (10%)
- [x] This repository created, with architecture documentation and diagrams
- [x] Stellar testnet environment configured (accounts, USDC trustlines) — `scripts/`, proven by the live E2E demo (`npm run demo:testnet`)
- [ ] Partner-facing Stellar flow specification (API schemas for the payout lifecycle)
- [ ] Initial compliance mapping
- [ ] Technical implementation plan published for SCF review

## Tranche 1 — MVP: Stellar USDC testnet adapter (20%) — target 2026-08-15
- [x] `stellar-usdc-adapter` v0 published (0.1.1 in the GitLab package registry: deposit detection — stream + poll modes, tx creation, confirmation monitoring)
- [x] `stellar_usdc` selectable as settlement rail in the partner API (sandbox) — verified E2E on testnet: payout `ebe3f03b4d26d9f7dab3c4caa6b051cb57ff61f047ff7182374030092af6847f` completed the full lifecycle; live deposit `a131dc8a749aff081859877f5ede5f5f7ee84965b1e2916402a680e28842a50f` detected, memo-matched, webhooked
- [x] Transaction state machine implemented + webhook lifecycle events (HMAC-signed, one dispatch per transition)
- [x] Internal dashboard view for Stellar transactions (admin CRM: payouts with state-machine history, detected deposits, recon CSV, stellar.expert links)
- [x] Basic reconciliation export (CSV: payouts + deposits)
- [x] Idempotency and retry logic (partner-key replay verified; crash-safe submission)
- [x] Recorded end-to-end demo (1:24 — live API payout → on-chain confirmation on stellar.expert → dashboard with state-machine history, deposits, reconciliation)

## Tranche 2 — Anchor Platform + SDP (30%) — target 2026-08-31
- [x] Anchor Platform on testnet (SEP-1/10/24) — live at devanchor.muney.cc, full SEP-10 auth verified (JWT issued), TLS issued
- [x] SDP integrated for bulk payout / remittance flows — live at devsdp.muney.cc; bulk disbursement paid on-chain E2E (tx 43b42e2c…, receiver got 4.5 USDC)
- [x] User SDK v0 + Merchant SDK v0 — published to registry, both cash flows E2E-verified
- [ ] Partner sandbox open for pilot partners
- [ ] Automated tests for the transaction state machine
- [x] Partner integration documentation (docs/partner-cookbook.md) + open-source Anchor + SDP deployment guides
- [ ] ≥2 pilot partner (or partner-simulation) end-to-end testnet flows

## Tranche 3 — Mainnet launch + pilot (40%) — target 2026-09-15
- [ ] USDC on Stellar mainnet deployment; production rail enabled
- [ ] Mainnet monitoring, alerting, production reconciliation
- [ ] El Salvador merchant pilot: 100 merchants onboarded/listed; activation by cohorts (5 → 15 → 30 → 50)
- [ ] ≥2 B2B partner flows in production or controlled production
- [ ] Verifiable mainnet transaction evidence
- [ ] Final developer documentation + integration cookbook + public demo page
- [ ] (Stretch) USDT0 corridor — a USDT rail on Stellar alongside USDC (see note below)

### Note — USDT0 (LayerZero-OFT USDT) corridor

[USDT0](https://developers.stellar.org/launch/usdt0) is Tether's USDT as a LayerZero
Omnichain Fungible Token: one unified USDT supply that mints/burns across chains rather
than fragmenting into wrapped versions. On Stellar it is a **classic asset**
(`USDT0`, issuer `GATISXX6…`, LayerZero EID 30600) — mechanically identical to how this
rail already settles USDC (trustline + memo payment + deposit detection).

Because the adapter is now **asset-agnostic** (`assetCode` + `assetIssuer` config; USDC is
just the default), running a USDT rail is a config change plus a trustline — the state
machine, memo correlation, crash-safe submit, and deposit watcher are all reused. This is
a natural fit for a USDT-settlement business: no USDC↔USDT swap on the Stellar leg.

**Use:** bridge USDT held on an OFT chain we already use (e.g. **Polygon**) to Stellar as
USDT0 (30s–3min, unified supply), then off-ramp locally — one balance, chain-abstracted.

**Constraints (why it is Tranche 3, not earlier):**
- **Mainnet only** — no USDT0 testnet, so it cannot run under the Tranche 1/2 testnet scope.
- **No Tron** — USDT0's 23 networks include Polygon/Ethereum/Arbitrum/Optimism but not Tron,
  where much LATAM USDT liquidity lives; a Tron pre-hop (or a separate bridge/route) is
  needed to feed it.
- **Issuer controls** — the USDT0 classic asset is `auth_revocable` + `auth_clawback_enabled`
  (freezable by the issuer); trustline required or payments fail `op_no_trust`; call
  `quoteOFT()` for per-route minimums (7-dp on Stellar, normalized to 6 cross-chain).
