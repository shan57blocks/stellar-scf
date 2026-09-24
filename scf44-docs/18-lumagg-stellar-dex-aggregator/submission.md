# LumAgg — Stellar DEX Aggregator: LumAgg— Stellar DEX Aggregator

Source: https://communityfund.stellar.org/submissions/recFRG56TbGtuXbMt (SCF #44, awarded $90.0K, category: End-User Application)

- Website: https://www.lumagg.xyz
- Architecture doc: https://github.com/Lum-Agg/stellar-dex-agg#architecture

## Links in the submission

- https://github.com/Lum-Agg/stellar-dex-agg#architecture
- https://github.com/Lum-Agg/stellar-dex-agg
- https://github.com/ligulfzhou/stellar-dex-agg
- https://stellar.expert/explorer/public/tx/a571b4617bc42594673ab22a496ef61c4fc66689a4f9cc29fd71dc7fb74ccb54

## Submission text (as published)

```text
Products & Services
LumAgg is an open-source (Apache 2.0), self-hostable liquidity aggregator and operator stack for Stellar. It discovers prices across Soroswap, Aquarius (volatile, stable, and CLMM), Phoenix, Sushi, Comet, and Stellar Classic SDEX, compares Classic path payments against Soroban routes, and returns the best execution—including split orders across multiple venues when beneficial.
Problem:
 Stellar liquidity is split across Classic SDEX and isolated Soroban AMMs. Single-pool routes often suffer avoidable slippage; integrators otherwise maintain separate venue adapters and routing logic.
Solution & impact:
 LumAgg provides a production routing layer (web + API) for route discovery, split optimization, and wallet-ready transaction building (
quote
 +
build_tx
) on Stellar mainnet—improving execution quality, increasing Soroban pool utilization, and giving wallets and dApps a single integration entry point.
Operator stack (post-MVP):
 Self-deployable atomic arbitrage via on-chain
round_trip_swap
, arb vault, and
crates/arbitrage
—increasing cross-venue turnover and price alignment on Stellar; not offered as open operator infra by competing aggregators.
Requested Budget
$90.0K
Traction Evidence
Current traction and validation (mainnet):
- Live frontend:
https://www.lumagg.xyz
- Live API:
https://api.lumagg.xyz
- Public repository:
https://github.com/ligulfzhou/stellar-dex-agg
What is already shipped:
- Mainnet quote and build_tx endpoints for Stellar routing.
- Multi-DEX coverage (Soroswap, Aquarius, Phoenix, Sushi, Comet, Aquarius CLMM, and Classic DEX comparison).
- Redis-backed worker publish architecture for pool-state freshness.
- Production fixes already deployed for route quality and tx validity (including classic path payment minimum output handling and split-route sanity filters).
Operational evidence:
- Continuous mainnet quote/build testing and regression scripts.
- Public technical docs and architecture notes in repository docs/.
Mainnet execution proof:
Successful aggregator swap via LumAgg UI/API — 3-leg split route (Aquarius + Soroswap), demonstrating end-to-end quote → build_tx → on-chain execution.
Tx:
https://stellar.expert/explorer/public/tx/a571b4617bc42594673ab22a496ef61c4fc66689a4f9cc29fd71dc7fb74ccb54
2 xlm-> 0.5297941 usdc
- 47.29%: xlm ->cody ->usdc
aquarius(CAOBPWLHSERILJTLOH4APQU3AXUGQBOKLWDEM64A4AZG6XFSZHBEREDW) → aquarius(CBN2N5L4UM5PPQE5UQNC3HVGT56TDQMAXMT3LVFMNN6XLFXZMCJY6KOU)
- 12.72%: xlm ->xcr ->usdc
aquarius(CCRDMKXCM3F7I4SRA6PAICMFOY3MIR67U5QMZFW2EGHAXYYN2NI7USH4) → aquarius(CAFZJZZ7ITZHUGNHC5YSOS47LR66JS64G7TQW43JN4IMDG5KZC4VA3DW)
- 39.99%: xlm ->xrf ->usdc
soroswap(CCH3CJZWG6UMW522ESP3UHL4DCZLNXZLUHKYG5GCGNG5HXRL4A6O4A23) → aquarius(CB7FKGSTHP75ORTIZGGMVUTQLEMVTSEOI4QORQPCABJSGTAATDFCE2YV)
Quote regression script:
./scripts/scf-quote-regression.sh
(in repo)
Tranche 1 (Deliverable Roadmap) - MVP
budget: $
26,000
 (part of $90,000 total request)
Note for reviewers
: Tranche 1 does NOT rebuild the shipped worker/router/API. It funds public differentiation evidence, integrator API readiness, swap UI completion, and analytics indexer v0 (early data collection).
[DELIVERABLE 1: EXECUTION EVIDENCE & VENUE DIFFERENTIATION PACK]
Budget: $1,500 (Grant-period expansion only — initial comparison docs exist pre-award as $0 evidence; this funds matrix growth + refresh through Jul 2026)
Brief description
: Verifiable differentiation vs Soroswap (live quote benchmark), Stellar Broker (open-source router adapter matrix — no Aquarius CLMM / Sushi CLMM in public router-contract), and wallets (xBull). Reproducible scripts and docs in repo: scripts/
scf-benchmark.sh
, docs/
scf-venue-comparison.md
, docs/
scf-benchmark-results.md
.
How to measure completion:
Benchmark table: ≥3 pairs × ≥3 sizes; document fair rows (e.g. XLM→USDC 100–1,000 XLM parity; 10 XLM multi-leg split; XLM→AQUA via Aquarius CLMM)
Stellar Broker CLMM gap cited from public GitHub (stellar-broker/router-contract src/types/
protocol.rs
)
Reviewer can re-run ./scripts/
scf-benchmark.sh
 locally
At least one documented split case (is_split=true) vs Soroswap single-route output
Estimated date of completion
: July 20, 2026
[DELIVERABLE 2: INTEGRATOR-READY API — KEYS, OPENAPI & ROUTING CONTROLS]
Budget: $9,000
 (Engineering: partner API keys, OpenAPI spec, routing query params — NOT a router rewrite)
Brief description
: Make the existing public API adoptable by wallets and dApps: partner API key flow, documented rate limits, OpenAPI spec for /quote, /build_tx, /tokens, /health, integrator quickstart, and optional quote param prefer_soroban (exclude Classic DEX when integrators need Soroban-only comparison).
How to measure completion
:
published integrator guide + OpenAPI (Swagger) in repo, linked from docs site
Documented API key issuance process
prefer_soroban (or equivalent) live on /quote with documented behavior
≥1 external developer completes quote + unsigned XDR build using docs only (feedback or PR)
Estimated date of completion
: July 31, 2026
[DELIVERABLE 3: SWAP UI — TOKEN METADATA, LOGOS, BALANCE & QUICK AMOUNTS]
Budget: $6,500
 (Engineering: server-side token metadata enrichment + frontend UX)
Brief description
: Complete retail swap UX gaps on
https://lumagg.xyz
: enrich /api/v1/tokens with logo URLs (metadata pipeline + fallbacks), show wallet spendable balance for input token, quick amount chips 25% / 50% / 75% / 100% (respect XLM reserve), tx explorer link on success, and basic slippage control.
How to measure completion:
≥50 routed tokens return logo or deterministic fallback via API
Connected wallet shows spendable token_in balance
Four percentage chips populate amount_in; disabled when disconnected
Mobile-responsive; quote → build_tx → sign flow unchanged
No regression to production API
Estimated date of completion
: July 31, 2026
[DELIVERABLE 4: ON-CHAIN ANALYTICS — INDEXER V0]
Budget: $9,000
 (Engineering: tx/event ingestion — dashboard UI in Tranche 3)
Brief description
: Start production analytics (NOT implemented today). Indexer ingests LumAgg aggregator contract invocations from Horizon/Soroban RPC: swap volume, tx count, function breakdown (split_swap vs round_trip_swap), unique wallet addresses, per-leg DEX attribution (Soroswap, Aquarius, Phoenix, Sushi, Comet). Store time-series for ≥30 days by Tranche 3.
How to measure completion
:
Indexer running against mainnet aggregator contract ID(s)
Internal or API export: daily volume, tx count, unique users, per-function counts
Volume attribution spec drafted in docs/
Data pipeline documented for Tranche 3 dashboard
Estimated date of completion
: July 31, 2026
Tranche 2 (Deliverable Roadmap) - Testnet
budget: $36,000 (part of $90,000 total request)
Note for reviewers
: Tranche 2 funds npm SDK release, first production-ready atomic arbitrage operator stack (code written post-submission, Jun 2026 — NOT in original application), and
integrator integration validation (≥2 paths, self-host or public API)
. Does NOT fund rebuilding swap frontend or pre-submission router/worker.
[DELIVERABLE 1: NPM TYPESCRIPT SDK & INTEGRATION EXAMPLES]
Budget: $11,000
 (Engineering: npm publish, types, CI, examples)
Brief description
: Publish packages/sdk to npm (semver, typed quote() / buildTx(), sub-route leg parsing). Ship CLI example + minimal Freighter dApp snippet. Optional thin Rust client stub in crates/sdk for parity.
How to measure completion
:
Public npm package (Apache-2.0)
≥2 working examples in repo
Documented third-party swap-in-under-30-min test
API reference aligned with /api/v1/quote and /api/v1/build_tx
Estimated date of completion: August 20, 2026
[DELIVERABLE 2: ATOMIC ARBITRAGE OPERATOR STACK — POST-SUBMISSION CODE → MAINNET]
Budget: $17,000
 (Engineering: vault mainnet deploy, arb bot validation, operator docs, testnet→mainnet path, RPC/mainnet testing budget)
Brief description
: Net-new since initial application (Jun 2026): crates/arbitrage scanner + contracts/vault (execute_round_trip) + aggregator.round_trip_swap integration. Grant completes operator-grade release (NOT greenfield router work): (1) Deploy arb-only vault on mainnet (initialize, deposit, add_caller) (2) Harden scanner: ARB_VAULT_CONTRACT, DRY_RUN → simulated → controlled live submit (3) Operator playbook: pooled capital, multi-caller gas-only bots, safety limits (4) Ecosystem effect: atomic arb increases cross-venue DEX pool turnover and price alignment
How to measure completion
:
Mainnet vault contract ID + runbook (contracts/vault/
README.md
)
Published operator doc in docs/ (self-deploy: aggregator + vault + bot)
≥10 successful DRY_RUN or RPC-simulated prepared txs with route labels
≥10 on-chain round-trip (round_trip_swap or vault.execute_round_trip) on mainnet or public testnet with documented tx hash(s)
Risk/limitations section documented (arb-only vault, not retail yield)
Estimated date of completion: August 31, 2026
[DELIVERABLE 3: INTEGRATOR INTEGRATION VALIDATION (≥2 PATHS)]
Budget:
 $8,000 (Engineering support + documentation)
Brief description:
 Validate
≥2 integrator adoption paths
 for the open-source stack. Integrators may use
self-hosted
 API (docker-compose)
or
 the public API (
api.lumagg.xyz
) with the npm SDK — hosted API is
not
 required.
Path A:
 In-repo reference client (swap UI on
lumagg.xyz
 or SDK demo app): quote → build_tx end-to-end.
Path B:
 External developer, reviewer, or community integrator completes the same flow using published docs (self-host
or
 public API); feedback or anonymized confirmation documented.
Deliverable output is
reproducible integration evidence
 (repo examples + integrator guide updates), not guaranteed SaaS onboarding.
How to measure completion:
Two paths documented with reproducible steps (repo links; Path B may be anonymized role or public demo/PR)
Each path: quote + build_tx working via SDK or REST (self-host or public API)
Self-host quickstart updated (docker-compose or equivalent) in integrator guide
≥1 documented “integrate in under 30 minutes” walkthrough
Feedback from Path B incorporated into SDK and integrator guide
Estimated date of completion:
 August 31, 2026
Tranche 3 (Deliverable Roadmap) - Mainnet
budget: $28,000
 (part of $90,000 total request)
Note for reviewers
: Tranche 3 funds public analytics dashboard, third-party smart contract audit (aggregator + vault), and grant close-out. Audit follows vault deploy and arb stack freeze in Tranche 2.
[DELIVERABLE 1: ANALYTICS DASHBOARD & VOLUME REPORT]
Budget: $8,000
 (Engineering: dashboard and/or public /stats API on Tranche 1 indexer)
Brief description: Ship user-facing and reviewer-facing analytics built on Deliverable 4 indexer: (1) Total swap volume (notional) and tx count (2) split_swap vs round_trip_swap (and vault.execute_round_trip when live) (3) Unique trader addresses (4) Per-DEX / per-pool leg attribution (5) Arb impact section: sample round-trip txs → pools touched (6) Optional: worker pool-coverage health (% CLMM pools with complete tick coverage)
How to measure completion:
Public stats page or documented /stats API + CSV export
≥30 days of indexed data (or since indexer start)
Volume attribution spec finalized in docs/
Sample grant report table: user swaps + arb txs → DEX volume
Estimated date of completion: October 5, 2026
[DELIVERABLE 2: THIRD-PARTY SMART CONTRACT AUDIT — AGGREGATOR + VAULT]
Budget: $16,000
 (External audit firm or Stellar Audit Bank co-pay + remediation engineering)
Brief description
: Professional security audit of mainnet aggregator (split_swap, round_trip_swap, DEX CPI auth) and arb vault (execute_round_trip, caller allowlist, fund flow). Remediate critical/high findings; upgrade WASM if required.
How to measure completion:
Signed audit scope + final report (or Audit Bank engagement letter + report)
All critical/high issues resolved or accepted with documented rationale
Post-remediation contract IDs / upgrade tx hashes recorded
Estimated date of completion: October 1, 2026
[DELIVERABLE 3: GRANT CLOSE-OUT — DEMO, SELF-HOST KIT & FINAL REPORT]
Budget: $4,000
 (Documentation, demo video, handoff)
Brief description
: Final SCF report; 5-minute demo (swap UI with logos/balance %, split route, analytics snapshot, arb/vault architecture); docker-compose self-host kit for integrators; Protocol 27 compatibility test checklist; 6-month maintenance plan.
How to measure completion
:
Final report markdown/PDF in repo with all deliverable links
Public demo video (quote → split → on-chain swap; arb architecture segment)
docker-compose or equivalent documented self-host path (API + worker + Redis)
P27 testnet regression notes after network upgrade
Maintenance plan: RPC, monitoring, upgrade path
Estimated date of completion: October 15, 2026
Team
Ligang Zhou
 — Solo full-time founder (full-stack). Soroban contracts, Rust routing & pool-state backend, Next.js swap UI, and mainnet ops.
Background:
 Atomic arbitrage and DEX routing systems on
Flare, Stellar, Solana, Sui, Aptos, and Fuel
 — price discovery, atomic execution, slippage controls, and production bot operations.
GitHub:
https://github.com/ligulfzhou
linkedin:
https://www.linkedin.com/in/ligangzhou/
Jackson (Jianhao Bi) — Frontend engineer. Swap UI (Next.js/TS), wallet integration, split-route display, SDK integration examples.
GitHub:
https://github.com/Billshimmer
LinkedIn:
https://www.linkedin.com/in/%E5%BB%BA%E6%B5%A9-%E6%AF%95-467967415/
ligulfzhou
```
