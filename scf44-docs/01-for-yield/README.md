# For Yield

For Yield (ForYield SAS, Paris) is building a yield vault on Stellar for European savers. A "vault" is a smart contract that takes deposits in a stablecoin (a token pegged to a currency, here USDC or EURC, the euro coin) and lends or trades them in other on-chain protocols to earn interest. It is aimed at wealthy, non-crypto-native clients (45-65 years old, EUR 500k+ net worth) and at French independent wealth advisors. The company says it filed for a MiCA licence (the EU crypto-asset law) with the French regulator AMF in April 2026, targeting approval in October 2026. Today it runs a pilot of about $7M on other chains (EVM/Base); this grant builds the Stellar version.

## What SCF #44 pays them to build

Total $144,000: 3 tranches of $48,000 (1,800 engineering hours at $80/hr).

- **Tranche 1 - MVP ($48,000)**
  - D1 Soroban YieldVault: USDC deposits, proportional shares, allocation to a Blend v2 lending pool, 200+ tests, >90% coverage ($20,000).
  - D2 Wallet onboarding: Stellar Wallets Kit for more wallets (xBull, Albedo, Lobstr, Ledger) plus DFNS embedded wallets from an email login, no seed phrase ($14,000).
  - D3 EURC via its SAC wrapper (SAC = Stellar Asset Contract, the adapter that lets a classic Stellar asset be used by contracts) ($14,000).
- **Tranche 2 - Testnet ($48,000)**
  - D4 DEX routing: swaps for rebalancing via Soroswap, Aquarius as fallback ($14,000).
  - D5 Multi-protocol allocator via DeFindex (Blend, Aquarius, Soroswap) ($16,000).
  - D6 Performance-fee module with high-water mark (fees only on new highs), paid in EURC, plus Soroban Events for a regulator audit trail ($18,000).
- **Tranche 3 - Mainnet ($48,000)**
  - D7 Mainnet deployment after a Certora audit, plus monitoring dashboard ($14,000).
  - D8a Allbridge cross-chain deposits from EVM/Solana ($6,000); D8b investor dashboard ($8,000).
  - D9 First production deposits: at least $1M in the mainnet vault and one full quarterly report ($20,000).

The Certora audit is paid by the Stellar LaunchKit audit credit, not the grant. If the licence slips, D9 deposits come from the team's own capital.

## How it works

- **YieldVault contract** (Rust, Soroban = Stellar's smart-contract platform). `deposit` pulls the asset and mints shares; `withdraw` burns shares and pays back pro-rata. Rounding favors the vault. The first deposit locks 1,000 "dead" shares to block a known price-inflation attack. Admin can pause.
- **Allocation**: if a Blend v2 pool is set at `initialize`, every deposit is supplied to Blend in the same transaction; interest raises the share price. Multi-protocol allocation via DeFindex is planned for Tranche 2.
- **Assets**: the contract works with any token. Testnet instances exist for USDC (with Blend), EURC via its SAC, and native XLM (public demo).
- **SwapRouter contract** (D4): swaps USDC/EURC with a minimum-output check (slippage protection) and per-pair fee accounting. Soroswap was removed on 2026-08-28 after the team was told it was compromised, so it now routes only through Aquarius (no fallback). Phoenix was rejected as a replacement.
- **Two ways in**: a browser wallet through Stellar Wallets Kit (web app at vault.for-yield.com, a Next.js static site), or a DFNS wallet created from an email (the `onboarding/` package) that signs through the DFNS API.
- **Audit trail**: every deposit and withdrawal emits a structured Soroban event, to be read by a compliance dashboard.
- Planned outside the grant: DFNS/Fireblocks custody, Elliptic or Notabene for Travel Rule checks, Sumsub for KYC (identity checks).

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recDqMIZnhmuWnq2f | submission.md | Tranches, budget, criteria |
| Evidence Data Room (PDF, June 2026) | architecture / pitch | https://drive.google.com/file/d/1iepVOIiSqV4DFtMhH8AxCQgJWOuQsTnP/view | architecture.pdf, architecture.txt | The submission's "architecture" link; mostly overview, traction, team. Points to the-arch.md for the technical design |
| Technical architecture (the-arch.md, v2 2026-08-04) | architecture | https://github.com/Foryield/soroban-yield-vault/blob/main/docs/the-arch.md | the-arch.md | Main technical doc: contracts, deployments, onboarding flows |
| Repo README | docs | https://github.com/Foryield/soroban-yield-vault | repo-readme.md | Testnet contract IDs, contract interface, roadmap |
| Reviewer evidence log index | evidence | https://github.com/Foryield/soroban-yield-vault/blob/main/docs/evidence/README.md | evidence-index.md | Per-deliverable files d1-d4 exist in `docs/evidence/`; d5, d6 listed but not in repo yet |
| D4 swap router design | spec | https://github.com/Foryield/soroban-yield-vault/blob/main/docs/plans/2026-07-22-d4-swap-router-design.md | d4-swap-router-design.md | Written when Soroswap was still a venue |
| Soroswap removal plan (French) | spec | https://github.com/Foryield/soroban-yield-vault/blob/main/docs/plans/2026-08-28-retrait-soroswap-aquarius-seul.md | plan-aquarius-only.md | Why the router is Aquarius-only; why Phoenix was rejected |
| SCF catch-up roadmap D1-D6 (French) | spec | https://github.com/Foryield/soroban-yield-vault/blob/main/docs/plans/2026-07-21-rattrapage-scf.md | plan-rattrapage-scf.md | Gaps reviewers found in D1 and the plan to fix them by 2026-09-30 |
| On-chain AUM Evidence (PDF) | evidence | https://drive.google.com/file/d/1um_iE789ZrMfnSC4bhg2vYY0qoh8gBq6/view?usp=sharing | — | Masked DeBank screenshots of the ~$7M pilot; images only, not saved |
| Demo video | demo | https://youtu.be/v2eI3zRoKs4 | — | "ForYield Demo video - Stellar", under 3 min |
| Testnet vault demo | demo | https://vault.for-yield.com | — | Live web app |
| Website | website | https://www.for-yield.com | — | Marketing page, no docs links |
| SCF project page | earlier SCF submission | https://communityfund.stellar.org/project/recOaZAQ1bJHcu2Oz | — | Lists only SCF #44; no earlier rounds |
| Other repos | code | https://github.com/Foryield | — | soroban-amm, solana-yield-vault, arc-yield-vault; not part of this grant |

## Gaps

- The pitch deck is "provided alongside" the data room, but no link to it is public.
- Staging pilot platform (foryield-frontend-staging.osc-fr1.scalingo.io) opens but needs a login.
- Company LinkedIn blocks automated access (HTTP 999), so it was not checked.
- Evidence files for D5 (allocator) and D6 (fees and audit trail) are listed in the evidence index but not yet in the repo.
- No written spec yet for the performance-fee module, DeFindex allocator, Allbridge or investor dashboard (later tranches).
