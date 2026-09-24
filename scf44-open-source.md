# SCF #44: Open-Source Code Record

Researched and re-verified 2026-09-24. The [official SCF #44 page](https://communityfund.stellar.org/awards/rec4FnYypcsKpBRB4) lists 41 awarded projects (Stellar's recap says 46; the other 5 are not shown). This file keeps only the 32 projects with public code. The 9 with no public code were removed.

Method:
- Links were taken from each official submission page (`https://communityfund.stellar.org/submissions/<id>`), including its GitHub button.
- Each project's website, team members' GitHub profiles, GitHub/GitLab search and npm were then checked.
- Every repo below was confirmed public through the GitHub or GitLab API on 2026-09-24. Its README was read to confirm it belongs to the project.

Status meanings:
- **Open**: the project's own code for this grant is public.
- **Partial**: some of the team's code is public, but not the Stellar part of this grant, or the license only allows reading it.

## Summary

| Status | Count |
|---|---|
| Open | 23 |
| Partial | 9 |

| # | Team | Award | Status | Main repo |
|---|---|---|---|---|
| 1 | For Yield | $144.0K | Open | [Foryield/soroban-yield-vault](https://github.com/Foryield/soroban-yield-vault) |
| 2 | Trustline | $133.6K | Open | [TrustLine-id/stellar-sdk](https://github.com/TrustLine-id/stellar-sdk) |
| 3 | Tokeshare | $133.0K | Open | [bim-finance-org/tokeshare-stellar-contracts](https://github.com/bim-finance-org/tokeshare-stellar-contracts) |
| 4 | Octarine | $129.7K | Open | [mystic-finance/Stellar-RFQ](https://github.com/mystic-finance/Stellar-RFQ) |
| 5 | Smart Treasury Account | $127.5K | Open | [Smart-Treasury-Account-STA/smart-contracts](https://github.com/Smart-Treasury-Account-STA/smart-contracts) |
| 6 | CredioLabs.AI (Untangled) | $125.0K | Open | [untangledfinance/oz-policy-builder](https://github.com/untangledfinance/oz-policy-builder) |
| 7 | Prism | $124.6K | Partial | [HeylmStoned/prism-stellar-earn](https://github.com/HeylmStoned/prism-stellar-earn) |
| 8 | Account Demolisher | $120.0K | Open | [bytemaster333/account-demolisher](https://github.com/bytemaster333/account-demolisher) |
| 9 | LumenWipe | $120.0K | Open | [LumenWipe/lumenwipe](https://github.com/LumenWipe/lumenwipe) |
| 10 | Etesia | $113.0K | Partial | [Etesia-Research/stellar_vault](https://github.com/Etesia-Research/stellar_vault) |
| 11 | ChimpDAO | $110.0K | Open | [Consulting-Manao/chimpdao-contracts](https://github.com/Consulting-Manao/chimpdao-contracts) |
| 12 | VERSO | $110.0K | Open | [WildorAP/VERSO-SDF](https://github.com/WildorAP/VERSO-SDF) |
| 13 | Fuul | $105.0K | Partial | [kuyen-labs/fuul-sdk](https://github.com/kuyen-labs/fuul-sdk) |
| 14 | ROZO | $98.0K | Open | [mpprouter/rozo-mpprouter](https://github.com/mpprouter/rozo-mpprouter) |
| 15 | Gateway.fm | $98.0K | Open | [gateway-fm/oz-policy-builder](https://github.com/gateway-fm/oz-policy-builder) |
| 16 | Passkey UI (SoroPass) | $95.0K | Open | [justmert/soropass](https://github.com/justmert/soropass) |
| 17 | Muney App | $95.0K | Open | [holamuney/muney-stellar-integration](https://gitlab.com/holamuney/muney-stellar-integration) (GitLab) |
| 18 | LumAgg | $90.0K | Open | [Lum-Agg/stellar-dex-agg](https://github.com/Lum-Agg/stellar-dex-agg) |
| 19 | ElementPay | $90.0K | Partial | [elementpayke/elementpay-business](https://github.com/elementpayke/elementpay-business) |
| 20 | JUMPA | $89.7K | Open | [official-jumpa/jumpa-web-app](https://github.com/official-jumpa/jumpa-web-app) |
| 21 | Azza | $89.0K | Partial | [Blocverse01/azza-fx-management-service](https://github.com/Blocverse01/azza-fx-management-service) |
| 22 | Ballast Re | $85.0K | Open | [chrisc999/ballast-re](https://github.com/chrisc999/ballast-re) |
| 23 | Lusty Finance | $82.5K | Open | [utkurock/Lusty](https://github.com/utkurock/Lusty) |
| 24 | Paycashless | $78.0K | Partial | [paycashless/qpi.js](https://github.com/paycashless/qpi.js) |
| 25 | Authline | $75.0K | Open | [theahaco/authline](https://github.com/theahaco/authline) |
| 26 | Blink | $75.0K | Partial | [JayWebtech/blink-sdk-example](https://github.com/JayWebtech/blink-sdk-example) |
| 27 | Offer-Hub | $74.0K | Open | [OFFER-HUB/offer-hub-monorepo](https://github.com/OFFER-HUB/offer-hub-monorepo) |
| 28 | Escala HQ (Embedded Collective Investment) | $70.0K | Partial | [escala-dev/collective-investment-api](https://github.com/escala-dev/collective-investment-api) |
| 29 | TroqPay | $60.0K | Partial | [troqpay/sdk](https://github.com/troqpay/sdk) |
| 30 | Policywright | $55.0K | Open | [kunaldrall29/policywright](https://github.com/kunaldrall29/policywright) |
| 31 | VRF-Soroban | $50.0K | Open | [NibrasD/Stellar-VRF](https://github.com/NibrasD/Stellar-VRF) |
| 32 | SAFU Protocol | $30.0K | Open | [mrkanchwala/safu-soroban](https://github.com/mrkanchwala/safu-soroban) |

## Details per project

Repo format: URL | language | stars | license | last push | what it is. "No license" means the repo has no license file, so reuse rights are not granted.

### 1. For Yield: EU MiCA Capital Layer for Soroban DeFi ($144.0K), Open
- Website: https://www.for-yield.com (vault app: vault.for-yield.com)
- https://github.com/Foryield/soroban-yield-vault | Rust | 0 | MIT | 2026-08-31 | Soroban vault contract: deposit, share minting, withdraw, pause, Blend v2 allocation. Linked from the submission.
- Notes: fee module and EURC wrapper are promised in later tranches.

### 2. Trustline: Unlock Onchain Finance for Institutions ($133.6K), Open
- Website: https://www.trustline.id. GitHub org: [TrustLine-id](https://github.com/TrustLine-id)
- https://github.com/TrustLine-id/stellar-sdk | Rust | 0 | MIT | 2026-09-03 | Soroban contract protection SDK
- https://github.com/TrustLine-id/stellar-validation-engine | Rust | 0 | GPL-2.0 | 2026-09-03 | validation engine using Trustline's oracle
- https://github.com/TrustLine-id/stellar-demo-app | TS | 0 | MIT | 2026-09-23 | SCF #44 end-to-end testnet demo
- https://github.com/TrustLine-id/stellar-poc-registry-escrow | TS | 0 | MIT | 2026-02-08 | proof-of-concept registry and escrow, linked from the submission

### 3. Tokeshare: LATAM Real-World Investing on Stellar ($133.0K), Open
- Website: https://tokeshare.co. GitHub org: [bim-finance-org](https://github.com/bim-finance-org)
- https://github.com/bim-finance-org/tokeshare-stellar-contracts | Rust | 0 | MIT | 2026-08-23 | Soroban real-world-asset contracts: compliance token, fixed-price sale with buyback
- https://github.com/bim-finance-org/tokeshare | TS | 0 | MIT | 2026-09-19 | app and frontend, linked from the submission

### 4. Octarine: RFQ for RWA Instant Redemptions ($129.7K), Open
- Website: http://octarine.finance (docs.octarine.finance). GitHub org: [mystic-finance](https://github.com/mystic-finance)
- https://github.com/mystic-finance/Stellar-RFQ | Rust | 0 | no license | 2026-09-01 | Soroban request-for-quote contracts, deploy scripts, docs. Linked from the submission.

### 5. Smart Treasury Account: Programmable Stellar Treasuries ($127.5K), Open
- GitHub org: [Smart-Treasury-Account-STA](https://github.com/Smart-Treasury-Account-STA)
- https://github.com/Smart-Treasury-Account-STA/smart-contracts | Rust | 0 | Apache-2.0 | 2026-09-10 | Soroban contracts with review and testnet docs, linked from the submission
- https://github.com/Smart-Treasury-Account-STA/sdk | TS | 0 | MIT | 2026-09-10 | SDK
- https://github.com/Smart-Treasury-Account-STA/dApp | TS | 0 | no license | 2026-09-11 | frontend
- https://github.com/Smart-Treasury-Account-STA/docs | TS | 0 | no license | 2026-09-11 | docs site

### 6. CredioLabs.AI: OZ Policy Builder ($125.0K), Open
- Website: https://crediolabs.ai, https://untangled.finance. GitHub org: [untangledfinance](https://github.com/untangledfinance)
- https://github.com/untangledfinance/oz-policy-builder | TS | 0 | MIT | 2026-09-23 | records a Soroban transaction and installs the smallest matching policy on an OpenZeppelin smart account
- https://github.com/untangledfinance/soroban-vault-contract | Rust | 0 | no license | 2026-01-28 | earlier vault contract
- Notes: kalepail/pollywallet, linked in the submission, is someone else's prototype this builds on.

### 7. Prism: One-Click Stellar Yield for EVM Users ($124.6K), Partial
- Website: prismfi.cc (demo: stellar.prismfi.cc/earn). GitHub user: HeylmStoned
- https://github.com/HeylmStoned/prism-stellar-earn | TS | 0 | no license | 2026-06-11 | Stellar integration and UI preview, linked from the submission
- Notes: README says the production frontend, backend and portfolio engine are private, and several features are "preview only".

### 8. Account Demolisher ($120.0K), Open
- Website: demolisher.saliht.xyz. GitHub user: bytemaster333
- https://github.com/bytemaster333/account-demolisher | TS | 1 | Apache-2.0 | 2026-08-25 | closes a Stellar account, with Soroban DeFi support

### 9. LumenWipe: Account Demolisher ($120.0K), Open
- Website: lumenwipe.com (docs.lumenwipe.com). GitHub org: [LumenWipe](https://github.com/LumenWipe)
- https://github.com/LumenWipe/lumenwipe | TS | 3 | Apache-2.0 | 2026-09-23 | closes any Stellar account and recovers the locked XLM without taking custody of it

### 10. Etesia: Risk Parity Vaults & Perp CTA on Stellar ($113.0K), Partial
- Website: etesiar.com. GitHub org: [Etesia-Research](https://github.com/Etesia-Research)
- https://github.com/Etesia-Research/stellar_vault | Rust | 0 | proprietary | 2026-09-19 | Soroban vault contracts
- https://github.com/Etesia-Research/etesia-portfolio-builder | JS | 0 | proprietary | 2026-09-19 | Next.js frontend
- Notes: code is public, but its "Proprietary Source Inspection License" only lets you read it. Copied DeFindex code keeps its GPL license.

### 11. ChimpDAO: Tap-to-Use Stellar DeFi Card ($110.0K), Open
- Website: chimpdao.xyz. GitHub org: [Consulting-Manao](https://github.com/Consulting-Manao)
- https://github.com/Consulting-Manao/chimpdao-contracts | Rust | 4 | BSD-3-Clause | 2026-09-01 | Soroban contracts
- https://github.com/Consulting-Manao/chimpdao-ios | Swift | 1 | BSD-3-Clause | 2026-04-09 | iOS app
- https://github.com/Consulting-Manao/chimpdao-nft | TS | 0 | BSD-3-Clause | 2026-04-11 | NFT app

### 12. VERSO: PEN/USD On/Off-Ramp, SDF Anchor Platform ($110.0K), Open
- Website: versotek.io (anchor.versotek.io). GitHub user: WildorAP
- https://github.com/WildorAP/VERSO-SDF | Python | 0 | no license | 2026-09-16 | Django + Polaris anchor. README: SEP-1 and SEP-10 done on testnet; SEP-24, SEP-38 and mainnet pending.

### 13. Fuul: Unlocking DeFi Incentives on Stellar ($105.0K), Partial
- Website: fuul.xyz (docs.fuul.xyz). GitHub orgs: [fuul-protocol](https://github.com/fuul-protocol), [kuyen-labs](https://github.com/kuyen-labs)
- https://github.com/kuyen-labs/fuul-sdk | TS | 0 | no license | 2026-08-21 | Fuul's SDK, published on npm as `@fuul/sdk`
- Notes: all public code is for other blockchains. The submission promises MIT-licensed Soroban contracts in Tranche 1; not published yet.

### 14. ROZO: ROZO Pay for AI Services via Stellar MPP ($98.0K), Open
- Website: rozo.ai. GitHub orgs: [mpprouter](https://github.com/mpprouter), [RozoAI](https://github.com/RozoAI)
- https://github.com/mpprouter/rozo-mpprouter | TS | 0 | BSD-2-Clause | 2026-09-23 | payment router for AI agents using Machine Payments Protocol (MPP)
- https://github.com/mpprouter/mpp-spec | – | 0 | MIT | 2026-09-23 | Stellar MPP spec, linked from the submission
- https://github.com/mpprouter/stellar-agent-wallet-skill | TS | 0 | no license | 2026-09-23 | Stellar USDC wallet for AI agents
- https://github.com/mpprouter/one-way-channel | Rust | 0 | Apache-2.0 | 2026-09-16 | Soroban payment channel. A fork of stellar-experimental/one-way-channel that ROZO continues to develop.
- https://github.com/RozoAI/rozo-intents-contracts | Rust | 3 | BSD-2-Clause | 2026-05-24 | cross-chain stablecoin intent contracts, including Stellar

### 15. Gateway.fm: OpenZeppelin Accounts Policy Builder ($98.0K), Open
- Website: gateway.fm. GitHub org: [gateway-fm](https://github.com/gateway-fm)
- https://github.com/gateway-fm/oz-policy-builder | Rust | 0 | Apache-2.0 | 2026-09-22 | records a transaction, works out the minimum permissions, generates a Soroban policy contract

### 16. Passkey UI: SoroPass Passkey SDK & UI Components ($95.0K), Open
- Website: soropass.dev. GitHub user: justmert
- https://github.com/justmert/soropass | TS | 2 | Apache-2.0 | 2026-09-21 | passkey SDK and UI components for Stellar smart accounts

### 17. Muney App: Stablecoin-to-Cash Layer Infrastructure ($95.0K), Open
- Website: muney.app. GitLab user: [holamuney](https://gitlab.com/holamuney) (linked from the submission)
- https://gitlab.com/holamuney/muney-stellar-integration | TypeScript | 0 | Apache-2.0 | 2026-09-03 | Stellar Anchor Platform config, user and merchant SDKs, Stellar USDC adapter, testnet demo scripts, docs

### 18. LumAgg: Stellar DEX Aggregator ($90.0K), Open
- Website: lumagg.xyz. GitHub org: [Lum-Agg](https://github.com/Lum-Agg)
- https://github.com/Lum-Agg/stellar-dex-agg | Rust | 2 | Apache-2.0 | 2026-09-21 | aggregator monorepo

### 19. ElementPay: Stablecoin Rails for the Global South ($90.0K), Partial
- Website: elementpay.net. GitHub org: [elementpayke](https://github.com/elementpayke)
- https://github.com/elementpayke/elementpay-business | TS | 0 | no license | 2026-09-03 | business dashboard frontend
- https://github.com/elementpayke/partner-docs | MDX | 0 | no license | 2026-09-22 | partner API docs
- Notes: no backend or Stellar code is public.

### 20. JUMPA: Chat-Native Multi-Chain Wallet Gateway ($89.7K), Open
- Website: jumpa.xyz. GitHub org: [official-jumpa](https://github.com/official-jumpa) (linked from the submission)
- https://github.com/official-jumpa/jumpa-web-app | TS | 2 | no license | 2026-09-24 | chat-based wallet web app. Has a Stellar module (`lib/chains/stellar`: accounts, sponsorship, DeFindex client, swaps).
- https://github.com/official-jumpa/jumpa | TS | 0 | no license | 2026-09-03 | Telegram trading bot with Stellar wallet, trustline and payment helpers

### 21. Azza: Scaling Azza Across Africa ($89.0K), Partial
- Website: useazza.com. GitHub org: [Blocverse01](https://github.com/Blocverse01) (linked from the submission)
- https://github.com/Blocverse01/azza-fx-management-service | TS | 0 | MIT | 2026-06-12 | Azza foreign-exchange service
- https://github.com/Blocverse01/azza-business-whatsapp-bot | TS | 0 | no license | 2025-07-25 | Azza WhatsApp bot
- Notes: no Stellar code in any public repo.

### 22. Ballast Re: baUSD Onchain Reinsurance RWA Vault ($85.0K), Open
- GitHub user: chrisc999
- https://github.com/chrisc999/ballast-re | Rust | 0 | Apache-2.0 | 2026-09-04 | Soroban vault token for baUSD, on testnet, not audited
- Notes: not linked from the submission. The owner is matched only by name (the CEO is Chris Comrie; the GitHub profile is blank).

### 23. Lusty Finance: DeFi Options Yield Protocol ($82.5K), Open
- Website: lusty.finance. GitHub user: utkurock
- https://github.com/utkurock/Lusty | TS | 3 | no license | 2026-09-19 | Next.js app plus `/contracts`; XLM options vaults (covered calls, cash-secured puts). Linked from the submission.

### 24. Paycashless: Payment Ops for Growing Businesses ($78.0K), Partial
- Website: paycashless.com (docs site links the org). GitHub org: [paycashless](https://github.com/paycashless)
- https://github.com/paycashless/qpi.js | TS | 0 | unclear | 2026-03-08 | QR payment-request encoder and decoder
- Notes: no Stellar code public. The submission promises a public Stellar reference repo later.

### 25. Authline: Trustline Onboarder & SDK ($75.0K), Open
- Website: testnet.authline.io. GitHub org: [theahaco](https://github.com/theahaco)
- https://github.com/theahaco/authline | TS | 0 | Apache-2.0 | 2026-09-15 | Stellar asset and trustline management app, linked from the submission

### 26. Blink: Blink for Merchants ($75.0K), Partial
- Website: useblinkapp.com. GitHub user: JayWebtech (team lead)
- https://github.com/JayWebtech/blink-sdk-example | JS | 0 | MIT | 2026-07-29 | playground for the `blink-checkout-sdk` npm package
- Notes: app, backend and contracts are not public.

### 27. Offer-Hub: Financial Access for Global Freelancers ($74.0K), Open
- Website: offer-hub.org. GitHub org: [OFFER-HUB](https://github.com/OFFER-HUB)
- https://github.com/OFFER-HUB/offer-hub-monorepo | TS | 38 | no license | 2026-09-09 | main platform
- https://github.com/OFFER-HUB/OFFER-HUB | TS | 2 | MIT | 2026-03-19 | payments orchestrator (Airtm + Trustless Work escrow on Stellar). The submission's `offer-hub` link redirects here.
- https://github.com/OFFER-HUB/OFFER-HUB-Frontend | TS | 3 | no license | 2026-09-23 | frontend

### 28. Escala HQ: Embedded Collective Investment via Soroban ($70.0K), Partial
- Website: escalahq.com. GitHub org: [escala-dev](https://github.com/escala-dev)
- https://github.com/escala-dev/collective-investment-api | JS | 0 | no license | 2026-06-16 | Express API outline (funds, proposals, milestones, disbursements), linked from the submission
- Notes: the services return hard-coded sample data. No Stellar or Soroban code yet.

### 29. TroqPay: Sell with Pix, Receive in USD ($60.0K), Partial
- Website: troqpay.com. GitHub org: [troqpay](https://github.com/troqpay) (linked from the submission)
- https://github.com/troqpay/sdk | TS | 0 | MIT | 2026-08-22 | client SDK for the TroqPay API
- https://github.com/troqpay/agent-plugin | TS | 0 | MIT | 2026-06-16 | MCP server and AI-agent plugin
- Notes: no Stellar code public.

### 30. Policywright: Record-to-Policy MCP + Agent Skill ($55.0K), Open
- GitHub user: kunaldrall29
- https://github.com/kunaldrall29/policywright | TS | 1 | MIT | 2026-09-21 | turns a past transaction into the tightest OpenZeppelin smart-account permission, with a dry-run check. Linked from the submission.

### 31. VRF-Soroban: Soroban VRF ($50.0K), Open
- Website: soroban-vrf-frontend.onrender.com. GitHub user: NibrasD
- https://github.com/NibrasD/Stellar-VRF | Rust | 0 | no license | 2026-09-23 | verifiable on-chain randomness (BLS12-381 + drand): contract, oracle worker, SDK, dashboard, example app. Linked from the submission.

### 32. SAFU Protocol: Stellar Wallet Protection Pools ($30.0K), Open
- Website: safustaking.com. GitHub user: mrkanchwala
- https://github.com/mrkanchwala/safu-soroban | Rust | 0 | Apache-2.0 | 2026-09-13 | Soroban protection-pool contract. README says it was built for SCF #44 in three tranches.
- https://github.com/mrkanchwala/safu-protocol | Solidity | 0 | unclear | 2026-06-27 | earlier EVM version, linked from the submission
