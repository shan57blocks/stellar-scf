# ElementPay

ElementPay is payment software for businesses in Africa and the Global South. It lets a business send invoices, collect payments, pay people out, and settle money into local rails such as mobile money (for example M-Pesa) or bank accounts, using stablecoins (crypto tokens pegged to the US dollar, like USDC). It also has a dashboard and APIs for tracking and reconciling payments (matching each payment to its invoice or payout). Today it runs on EVM chains (Ethereum-style chains such as Base). SCF #44 pays them to add Stellar. SCF #44 award: $90.0K.

## What SCF #44 pays them to build

From the submission page:

- **Tranche 1 – MVP ($18,000).** Stellar Wallets Kit (a library to connect Stellar wallets), Stellar accounts and asset transfers in the backend, transaction monitoring, and mapping Stellar transaction IDs to ElementPay invoices, payouts, customers and settlements. Proof: working wallet connect, test transactions, and Stellar activity shown in the dashboard.
- **Tranche 2 – Testnet ($27,000).** Stellar Disbursement Platform (SDP, a Stellar tool for bulk payouts) for payout batches, status tracking, webhooks (automatic HTTP notifications) and reconciliation. Aquarius (a Stellar liquidity/AMM protocol) for liquidity and routing data.
- **Tranche 3 – Mainnet ($35,999).** CCTP (Circle's Cross-Chain Transfer Protocol, which moves native USDC between chains) between Stellar and other chains, end-to-end Stellar invoicing, collections and payouts, pilot businesses, technical docs, and a mainnet or production-equivalent launch.

Note: the architecture document in their evidence folder has a different, later plan. It totals $69,999 and reorders the work: T1 foundation + Soroban escrow ($31,200), T2 CCTP + Aquarius ($12,000), T3 SDP + pilots + launch ($26,799). It says it was revised after panel feedback.

## How it works

- **Existing system:** a Python async backend (the "Element Aggregator") with MySQL. It routes each order to a payout partner (M-Pesa, SasaPay, Cellulant and others). An EVM escrow contract (`OrderManagement.sol` on Base) holds funds with three steps: create, settle, refund. A Next.js business dashboard sits on top.
- **New Stellar module ("StellarManager"):** built beside the EVM module, not inside it. Stellar uses different addresses (G... keys), assets (issuer + code) and needs trustlines (an account must opt in before it can hold USDC). Orders get a "chain" field so Stellar orders carry Stellar-style data.
- **Soroban escrow:** the plan in the architecture doc ports the EVM escrow contract to Soroban (Stellar's smart contract platform) on testnet in T1. Mainnet use waits for a security audit.
- **Monitoring:** a Stellar transaction monitor (Horizon streaming plus Soroban events) reuses their existing event-listener pattern and updates the dashboard, APIs and webhooks.
- **Stellar building blocks:** Wallets Kit (wallet connect), SDP (bulk payouts, self-hosted), Aquarius (liquidity data), CCTP (USDC across chains). The Anchor Platform (SEPs) is out of scope.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/rech5LZCLUxZCNwaa | submission.md | Tranches, budgets, completion criteria |
| Architecture doc linked in submission | architecture | https://docs.google.com/document/d/1CvOux9kW4VyKa84TgohxSKK_grFUPNvK/edit | — | No longer opens (404/410) |
| ElementPay Stellar Technical Architecture (.docx, evidence folder) | architecture | https://drive.google.com/file/d/1dNxRmPmUC3fR1VZghoqyqJUW2X4WDniZ/view | architecture.md | Revised plan, $69,999 total, itemized budget |
| ElementPay Stellar Backend Infrastructure Plan (.docx, evidence folder) | spec | https://drive.google.com/file/d/18-EwvzaVWZ7sm4sQNnSjlFK10Jk-C5gP/view | backend-infrastructure-plan.md | Services, tables, workers, state machines; diagrams not copied |
| Evidence folder | pitch | https://drive.google.com/drive/folders/1IoyP5JofRPQZ-8lEk6034BqVs1HyoLz1 | — | Also has dashboard PDFs, a Swipelux PDF, "bloxifi" PDFs |
| Ecosystem support (evidence folder) | pitch | https://docs.google.com/document/d/10yPKzIijPo9VHNXx1_VNgtu5jyDbuIrmS7vmAKvq5NA/edit | ecosystem-support.md | Links to Base, Lisk/AyaHQ, XFounders recognition |
| Mboka B2B Dashboard note (evidence folder) | demo | https://docs.google.com/document/d/1NQBNnuu4ZJ5KymCYClDOxoHxy7AOhIPZqIFt2H1A9_M/edit | — | Test login for the private beta dashboard; not copied |
| Dashboard demo video | demo | https://drive.google.com/file/d/1MZ-AvY9dUkFe6XhmfKCiUERIFFK07pvR/view | — | .mov file |
| Business dashboard (beta) | demo | https://elementpay-business.vercel.app | — | Needs login |
| Partner API docs site | docs site | https://elementpay.mintlify.app | — | On/off-ramp API; no Stellar content |
| Partner API docs repo | docs site | https://github.com/elementpayke/partner-docs | — | OpenAPI spec, guides, KNOWN_GAPS.md |
| Fiat ↔ stablecoin integration guide | spec | https://github.com/elementpayke/partner-docs/blob/main/docs/integration-fiat-stablecoin.md | partner-api-integration.md | Quote → accept order flow; no Stellar |
| Order flow doc | spec | https://github.com/elementpayke/elementpay-business/blob/master/ORDER_FLOW.md | — | Dashboard backend order API |
| elementpay-business repo | code | https://github.com/elementpayke/elementpay-business | — | Business dashboard frontend |
| Website | docs site | https://elementpay.net | — | Product site |
| XFounders portfolio page | blog | https://x-founders.com/portfolio/tproduct/1617461831-996079597582-elementpay | — | Accelerator listing |
| AyaHQ incubation page | blog | https://www.ayahq.com/incubation | — | Incubation programme listing |

## Gaps

- The architecture link in the submission is dead (404/410). A copy with a matching name was found in the evidence folder and saved.
- That copy's budget ($69,999, different tranche order) does not match the submission page ($90K). Which plan is binding is not stated.
- No backend or Stellar code is public. GitHub code search found no Stellar mentions in the public repos, and the docs site has none.
- No earlier SCF submissions are listed on the project page.
- No pitch deck or audit was found.
