# ROZO - One Tap to Pay

ROZO builds a payment layer on Stellar. It lets AI agents (programs that act for a user) and developers pay for AI, data, and blockchain services with USDC on Stellar. USDC is a digital dollar. There is no card and no sign-up. Examples of these services are OpenAI, Anthropic, Dune, and CoinGecko. The main product is **MPP Router**, a public API. A paid call first gets an "HTTP 402 Payment Required" reply. The buyer pays in USDC, then gets the result. The product is for agent builders and developers. It is also for service providers who want to be paid by agents. Earlier SCF grants paid for ROZO's cross-chain USDC payment network ("ROZO Intents").

## What SCF #44 pays them to build

Total award: $98,000. The submission lists these tranches:

- **Tranche 1 — MVP ($19,600).**
  - An open payment spec: x402 support, an MPP session payment format, a service catalog format, receipt and refund rules, and open provider registration.
  - 10 services verified payable from Stellar USDC: 6 AI inference and 4 blockchain or data. Agents or wallets pay per call, and each payment is recorded on-chain.
- **Tranche 2 — Testnet ($29,400).**
  - The spec as a tagged release.
  - Automatic refunds when a paid call does not deliver.
  - A public metrics dashboard.
  - An independent security review.
- **Tranche 3 — Mainnet ($39,200).**
  - At least one provider that is not ROZO runs its own server and gets paid to its own key. This is a hard gate for the payout.
  - Self-serve provider onboarding with no manual approval.
  - A final report.

## How it works

- **Buyers.** An agent or person uses a buyer SDK or an agent "skill" (a plug-in for AI agents), such as the Stellar Agentic Wallet on ClawHub. It calls a service, gets an HTTP 402 challenge, and pays.
- **Two payment types:**
  - **x402** is the standard Stellar x402 flow, for fixed-price calls. The buyer signs a Soroban authorization. A "facilitator" (a service that settles the payment, e.g. Coinbase or OpenZeppelin Relayer) checks it and settles it.
  - **MPP** (Machine Payments Protocol) is ROZO's extension. Its first form is a single USDC payment with the challenge's one-time number in the transaction memo. Its second form is a "session" for calls whose cost is only known afterwards, like AI output tokens. The buyer sets a maximum budget, and the final receipt records the actual amount used.
- **Payment channels.** Sessions use a Soroban payment channel contract. This is a contract that holds the buyer's deposit and pays out against signed "vouchers". It is a fork of `stellar-experimental/one-way-channel`. If the router stops working, the buyer can take back unused money after a waiting period of 100 ledgers (about 8 to 10 minutes).
- **MPP Router.** ROZO's first operator is a Cloudflare Worker (a small server program running on Cloudflare) at `apiserver.mpprouter.dev`. It provides:
  - a service catalog;
  - paid proxy routes;
  - a public settlement ledger;
  - automatic refunds with signed receipts when a paid call does not deliver.
- **Other providers.** They register through public check, register, and verify endpoints. Verification makes one small real payment. According to ROZO's multi-operator report, three third-party operators are live: Agent402, Stellar Indexer by Creit Tech, and Mercury Data.
- **Safety rules (spec section 5).**
  - An AI model never builds a payment. The SDK builds it from the challenge fields.
  - Spend caps, nonces that work only once, expiry times, and refund-on-failure rules apply.
- **Quality metrics.** Latency, price, and success and refund rates are published, including on Dune dashboards.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recgILHYysr76AfCt | submission.md | |
| mpp-spec "Technical Doc" (README) | architecture | https://github.com/mpprouter/mpp-spec | architecture.md | Covers system overview, payment paths, fields, pricing, security, metrics, and acceptance tests |
| MPP Router spec v0.1 index (docs/spec) | spec | https://github.com/mpprouter/rozo-mpprouter/tree/main/docs/spec | mpprouter-spec-index.md | Index of 6 spec files (x402, MPP session, catalog, provider registration, receipts and refunds, public ledger). Tagged `v0.1.0-tranche1` / `v0.2.0-tranche2` |
| MPP Router repo README | docs | https://github.com/mpprouter/rozo-mpprouter | mpprouter-readme.md | Channel refund steps and automatic non-delivery refunds |
| Multi-operator report | status report | https://github.com/mpprouter/rozo-mpprouter/blob/main/docs/multi-operator-report.md | multi-operator-report.md | Third-party operators and the self-serve onboarding flow (Tranche 3 evidence) |
| Security audit index | audit | https://github.com/mpprouter/rozo-mpprouter/blob/main/audit/README.md | audit-index.md | Scope, pre-audit, and a summary of the HackenProof audit |
| HackenProof audit report (MPP Router), 2026-09-11 | audit | https://github.com/mpprouter/rozo-mpprouter/releases/tag/v0.2.1-tranche2 | — | PDF is 29 MB (over the 20 MB limit), not copied. Summary: 10 findings, 0 critical, 0 high, 1 medium, 3 low, 6 informational, all resolved |
| Hacken audit of ROZO Intents v2 (Mar 2026) | audit | https://hacken.io/audits/rozo/sca-rozo-sdf-audit-mar2026/ | — | Returns 403 to scripts (bot block), could not verify content. Linked in the submission and repo README |
| Live service catalog | demo | https://apiserver.mpprouter.dev/v1/services/catalog | — | Live |
| OpenAPI description | spec | https://apiserver.mpprouter.dev/openapi.json | — | Live |
| MPP Router website | website | https://www.mpprouter.dev/ | — | |
| ROZO AI services page | website | https://www.rozo.ai/aiservices | — | |
| Stellar Agent Wallet skill repo | docs | https://github.com/mpprouter/stellar-agent-wallet-skill | — | Includes `references/` with x402 and MPP charge specs |
| one-way-channel (Soroban payment channel) | code | https://github.com/mpprouter/one-way-channel | — | The audit target |
| ClawHub skills | demo | https://clawhub.ai/shawnmuggle/stellar-agentic-wallet, https://clawhub.ai/shawnmuggle/rozo-intents-skills, https://clawhub.ai/shawnmuggle/mpprouter-discover | — | |
| Dune dashboards | analytics | https://dune.com/rozointents/stellar, https://dune.com/payments/rozo | — | |
| SCF #44 demo video | demo | https://www.youtube.com/watch?v=YzIgdhryk60 | — | "SCF44 ROZO: Intent Based Pay for AI Services via Stellar MPP & x402" |
| Pitch deck (DocSend) | pitch | https://docsend.com/view/wradjhfdybhrqcba | — | Returns 403 to scripts. DocSend usually asks for an email address, so not opened |
| SCF #43 submission, "Pay for Claude & ChatGPT via Stellar" (not awarded, $150K) | earlier SCF submission | https://communityfund.stellar.org/submissions/recrCfz6tNAqdM3Ho | scf43-submission.md | Earlier version of the AI-payments idea (natural-language checkout, OpenRouter, cashback token) |
| SCF #43 demo video | demo | https://www.youtube.com/watch?v=8vlM4SOB2fI | — | |
| SCF #38 submission, "Visa layer for stablecoins" (awarded, $150K) | earlier SCF submission | https://communityfund.stellar.org/submissions/rec5Y2MKyAzTJ6flK | scf38-submission.md | Cross-chain USDC pay-in and settlement on Soroban |
| ROZO Intents Stellar design doc | architecture (SCF #38 work) | https://github.com/RozoAI/rozo-intents-contracts/blob/main/docs/design/STELLAR.md | scf38-intents-stellar-design.md | The repo also has DESIGN, FUND_FLOW, and v2 design docs |
| SCF #38 demo videos | demo | https://youtu.be/hLIeRehjd5M, https://www.youtube.com/shorts/EB7DZY_bZr4 | — | |
| rozo-tap-to-pay, MugglePay repos | code | https://github.com/RozoAI/rozo-tap-to-pay, https://github.com/MugglePay/mugglepay | — | Linked as the team's earlier work |

## Gaps

- The pitch deck (DocSend) and the Hacken Intents audit page block automated access. Their content could not be checked.
- The HackenProof audit PDF is 29 MB, so it is linked, not copied.
- The submission lists no separate whitepaper. The mpp-spec README and the `docs/spec` files are the design documents.
