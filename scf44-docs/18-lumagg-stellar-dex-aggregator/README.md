# LumAgg — Stellar DEX Aggregator

LumAgg is a swap-price finder for Stellar. A "DEX" is a decentralized exchange, a place on the blockchain where you can swap one token for another. Stellar has many of them (Soroswap, Aquarius, Phoenix, Sushi, Comet, and the built-in "Classic" exchange). LumAgg checks all of them, finds the best route, and can split one trade across several exchanges to get a better price. It is for wallets and apps that want one simple API for swaps, and for people swapping on the website lumagg.xyz. It is open source (Apache 2.0) and can be self-hosted. The team is a solo founder (Ligang Zhou) plus a frontend engineer.

## What SCF #44 pays them to build

Total award: $90,000, in three tranches (a "tranche" is one payment tied to a set of deliverables). The router, API and website already existed before the award; the grant funds work on top of them.

- **Tranche 1 — MVP, $26,000 (target Jul 31, 2026)**
  - Benchmark pack comparing LumAgg with Soroswap, Stellar Broker and wallets ($1,500).
  - Integrator-ready API: partner API keys, OpenAPI spec, rate limits, a `prefer_soroban` option ($9,000).
  - Swap website improvements: token logos, wallet balance, 25/50/75/100% buttons, slippage control ($6,500).
  - Analytics indexer v0: records swap volume, transactions and users from the LumAgg contract ($9,000).
- **Tranche 2 — Testnet, $36,000 (target Aug 31, 2026)**
  - TypeScript SDK on npm, with examples ($11,000).
  - Atomic arbitrage "operator stack": a vault contract on mainnet, a hardened arbitrage bot, and an operator guide ($17,000).
  - Proof that at least two integrators can use the API/SDK from the docs ($8,000).
- **Tranche 3 — Mainnet, $28,000 (target Oct 15, 2026)**
  - Public analytics dashboard / stats API with 30+ days of data ($8,000).
  - Third-party security audit of the aggregator and vault contracts ($16,000).
  - Close-out: demo video, self-host kit, Protocol 27 checks, 6-month maintenance plan, final report ($4,000).

## How it works

- **Market-data worker** (Rust): finds pools on each DEX and keeps their current prices in Redis (a fast in-memory database). It watches every new Stellar ledger (a ledger is one block of transactions, about every 5 seconds) through Soroban RPC, and refreshes only the pools that changed. Only this worker writes to Redis.
- **API server** (Rust, stateless, can run many copies): answers `/quote` and `/build_tx`. It searches paths (up to 3 hops), prices each path with local pool math, and uses a split optimizer (Brent's method, a numeric search) to decide whether to split the trade across paths. It also compares against the Classic DEX via Horizon (Stellar's standard API) as a benchmark.
- **Aggregator contract** (a Soroban smart contract on mainnet): runs a multi-leg swap in one atomic transaction (all legs succeed or none do), with `split_swap` and `round_trip_swap`. `build_tx` returns an unsigned transaction the user signs in their own wallet.
- **Vault contract + arbitrage bot**: for operators. The vault holds trading money; allowlisted bot accounts run round-trip trades (XLM → token → XLM) through the aggregator when profitable.
- **Analytics indexer**, **TypeScript SDK** (`@lumagg/sdk`) and a **web frontend** at lumagg.xyz.
- A single-process "embedded" mode exists for self-hosting without Redis.

Stellar features used: Soroban smart contracts, Soroban RPC events, Horizon path payments (Classic DEX), Stellar Asset Contracts (SAC) for tokens.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recFRG56TbGtuXbMt | submission.md | Official proposal, $90K |
| SCF project page | earlier SCF submission | https://communityfund.stellar.org/project/reczBHQOhk4JLhpnp | — | Lists only SCF #44; no earlier rounds |
| Repo README (Architecture section) | architecture | https://github.com/Lum-Agg/stellar-dex-agg#architecture | architecture.md | The architecture link in the submission |
| Pool state architecture | architecture | https://github.com/Lum-Agg/stellar-dex-agg/blob/main/docs/pool-state-architecture.md | pool-state-architecture.md | How pool prices stay fresh |
| Original $100K application draft | requirements | https://github.com/Lum-Agg/stellar-dex-agg/blob/main/docs/scf-build-form-draft.md | original-100k-application-draft.md | First version of the SCF #44 ask, before resubmission |
| Resubmission budget ($90K) | requirements | https://github.com/Lum-Agg/stellar-dex-agg/blob/main/docs/scf-resubmission-budget.md | scf-resubmission-budget.md | Explains what changed from $100K to $90K |
| Integrator guide | spec | https://github.com/Lum-Agg/stellar-dex-agg/blob/main/docs/integrator-guide.md | integrator-guide.md | quote → build_tx → sign flow, API keys |
| API reference | spec | https://github.com/Lum-Agg/stellar-dex-agg/blob/main/docs/api-reference.md | api-reference.md | |
| OpenAPI spec | spec | https://github.com/Lum-Agg/stellar-dex-agg/blob/main/docs/openapi.yaml | — | |
| Audit scope | audit | https://github.com/Lum-Agg/stellar-dex-agg/blob/main/docs/audit-scope.md | audit-scope.md | Scope and budget only; no audit report yet |
| Tranche 1 completion form draft | requirements | https://github.com/Lum-Agg/stellar-dex-agg/blob/main/docs/tranche-1-completion-form.md | tranche-1-completion-form.md | Team's own evidence write-up; contains TODOs |
| Tranche 2 completion form draft | requirements | https://github.com/Lum-Agg/stellar-dex-agg/blob/main/docs/tranche-2-completion-form.md | tranche-2-completion-form.md | |
| Final report (draft) | requirements | https://github.com/Lum-Agg/stellar-dex-agg/blob/main/docs/scf-final-report.md | scf-final-report-draft.md | Deliverable index, mainnet contract IDs |
| Docs site (GitBook) | docs site | https://lumagg.gitbook.io/ | — | Canonical public docs |
| Legacy docs page | docs site | https://lumagg.xyz/docs | — | |
| Code repo | docs site | https://github.com/Lum-Agg/stellar-dex-agg | — | Rust monorepo; also many design specs in `docs/superpowers/specs/` |
| Older repo | docs site | https://github.com/ligulfzhou/stellar-dex-agg | — | Founder's personal repo linked in the submission |
| Swap website | demo | https://www.lumagg.xyz | — | |
| Public API | demo | https://api.lumagg.xyz | — | `/api/v1/health` returns 200 |
| Stats page | demo | https://lumagg.xyz/stats | — | |
| npm SDK `@lumagg/sdk` | spec | https://registry.npmjs.org/@lumagg/sdk | — | Latest 0.3.0; npmjs.com page blocks scripts (403) but package exists |
| Mainnet swap proof | demo | https://stellar.expert/explorer/public/tx/a571b4617bc42594673ab22a496ef61c4fc66689a4f9cc29fd71dc7fb74ccb54 | — | 3-leg split swap from the submission |

## Gaps

- No demo video link yet: the tranche completion drafts still say "TODO: paste video URL".
- No audit report yet (planned for Tranche 3).
- `https://api.lumagg.xyz/api/v1/stats` returned HTTP 525 (server TLS error) when checked; the web stats page loads.
- `docs.lumagg.xyz` (planned custom docs domain) does not resolve; use lumagg.gitbook.io.
- No pitch deck or whitepaper found.
