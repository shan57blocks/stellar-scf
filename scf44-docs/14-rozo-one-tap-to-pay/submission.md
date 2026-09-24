# ROZO - One Tap to Pay: ROZO Pay for AI Services via Stellar MPP

Source: https://communityfund.stellar.org/submissions/recgILHYysr76AfCt (SCF #44, awarded $98.0K, category: Financial Protocols)

- Website: https://www.rozo.ai/aiservices
- Architecture doc: https://github.com/mpprouter/mpp-spec

## Links in the submission

- https://github.com/mpprouter/mpp-spec
- https://dune.com/rozointents/stellar
- https://github.com/RozoAI/rozo-tap-to-pay
- https://github.com/MugglePay/mugglepay

## Submission text (as published)

```text
Products & Services
ROZO builds a permissionless merchant layer on Stellar that lets agents and developers pay for AI, data, and blockchain infrastructure services with Stellar USDC through x402 and MPP payments, without cards or registration on the payment path.
This submission funds five deliverables:
Open payment/interface spec for Merchant onboarding
 —
x402
 compatibility, an MPP/session-payment dialect, service catalog format, receipt/refund rules, and a permissionless provider-registration format.
Verified merchant network. Starting with 10 services verified payable from Stellar USDC, each with a reviewer-reproducible paid call recorded publicly.
AI inference: OpenAI, Anthropic, OpenRouter, Google Gemini, DeepSeek, Groq
Blockchain/data: Alchemy, Dune, CoinGecko, Quicknode)
Per-provider public metrics (p95 latency, price, cached-call discount, success/refund rate, cost per comparable task) so buyers can compare before paying.
Two payment shapes are supported:
x402
 for fixed-price calls, and
MPP Session payments
 for variable-cost calls. the buyer opens a bounded budget and the final receipt records actual usage. This solves the one thing exact x402 cannot: AI calls are priced by what the result consumed (output tokens, duration, data size), but exact x402 must fix the price before the result exists.
ROZO is the first intent operator (solver / filler), not the gatekeeper: under the open spec any service can be served by multiple independent providers, each running its own server and holding its own keys.
Requested Budget
$98.0K
Traction Evidence
Prior delivery on Stellar (SCF #38, live and audited):
95% of cross-chain orders into Stellar settle in ~20 seconds, for over 3000+ txs.
1,200+ Stellar wallets
Integrations with Defindex, Soroswap, LOBSTR, Mykobo, and CCTP
Audited
https://hacken.io/audits/rozo/sca-rozo-sdf-audit-mar2026/
Public Dune dashboard:
https://dune.com/rozointents/stellar
Current product evidence (the new demand this grant targets):
MPP Router live — service catalog:
https://apiserver.mpprouter.dev/v1/services/catalog
Stellar Agentic Wallet tested end-to-end:
https://clawhub.ai/shawnmuggle/stellar-agentic-wallet
1,400+ combined skill downloads across ROZO intent/payment skills on ClawHub:
-
https://clawhub.ai/shawnmuggle/rozo-intents-skills
-
https://clawhub.ai/shawnmuggle/stellar-agentic-wallet
-
https://clawhub.ai/shawnmuggle/mpprouter-discover
60-user human top-up pilot: 75 orders, ~$1K GMV, 40% repurchase in a discounted cohort
Tranche 1 (Deliverable Roadmap) - MVP
Brief description:
Design the open AI-payment protocol on Stellar and prove it end-to-end: open spec, and 10 services verified payable from Stellar USDC (6 AI inference + 4 blockchain/data)
How to measure completion:
Deliver 10 popular AI services or blockchain infra (for example OpenAI, Anthropic, OpenRouter, Google Gemini, DeepSeek, Groq, Alchemy, Dune, CoinGecko, Quicknode)
Stellar agents or wallets can pay per call and record on chain.
Budget:
 $19,600 (20%)
Tranche 2 (Deliverable Roadmap) - Testnet
Brief description:
Drive deeper real usage across the verified services, ship receipt/refund rules and per-provider service-quality metrics, and publish an independent security review.
How to measure completion:
Spec published at a tagged release
Refund triggers on non-delivery per published rules
Publish a public metrics dashboard v1 for the agentic payments on Stellar.
Budget:
 $29,400 (30%)
Tranche 3 (Deliverable Roadmap) - Mainnet
Brief description:
Prove the protocol is open, not single-operator: onboard at least one non-ROZO intent provider, ship provider-onboarding tooling and service-quality routing, and publish the final report.
How to measure completion:
A non-ROZO provider runs its own server and serves live paid calls settling to a non-ROZO key (hard payout gate);
self-serve onboarding works without manual approval;
Budget:
 $39,200 (40%)
Team
We have a solid technical and financial academic background and rich experience in crypto payment.
Shawn writes the code – studied CS in Stanford, founded multiple companies (Redshift, Chainsights, MugglePay). He researched at Virtual Human interaction lab at Stanford. You can check out the codebase here: <
https://github.com/RozoAI/rozo-tap-to-pay
>
LinkedIn:
https://www.linkedin.com/in/shawn-yu-37498b40/
Sky co-founded MugglePay with Shawn and leads operations at Rozo. She worked at American Express. She brings deep crypto experience from her time as a blockchain researcher at the Tron Foundation. Sky
co-founded
 MugglePay with Shawn in 2019,
helping
 over 3,000 merchants accept crypto. MugglePay has processed $50M+ per month on blockchain.
LinkedIn:
https://www.linkedin.com/in/sky-h-88811b118/
Sky Muggle
Shawn Yu
```
