# Paycashless: Payment Ops for Growing Businesses

Source: https://communityfund.stellar.org/submissions/recnS4XjkADmGtmDb (SCF #44, awarded $78.0K, category: End-User Application)

- Website: https://paycashless.com
- Architecture doc: https://docs.google.com/document/d/1ogK_j2kVoB3tLFPgYxis8aqf16dM4rI7-TB1BRa2vCQ/edit?tab=t.0#heading=h.4v3xmchx5o0w

## Links in the submission

- https://docs.google.com/document/d/1ogK_j2kVoB3tLFPgYxis8aqf16dM4rI7-TB1BRa2vCQ/edit?tab=t.0#heading=h.4v3xmchx5o0w
- http://docs.paycashless.com

## Submission text (as published)

```text
Products & Services
Paycashless Merchant Account
What: A live payments and financial-management platform for Nigerian SMEs. It puts everything a business needs to accept money, send money, manage cash flow, and access working capital into one merchant account: Digital Collections (POS, online checkout, pay-with-transfer, virtual accounts), Invoicing, Payouts, Working Capital, and Money Management & Insights. This SCF Build puts Stellar underneath all of it as the settlement and wallet layer, and that is what unlocks embedded merchant wallets, multi-chain balance consolidation, Naira off-ramps with cash pickup, on-chain working capital, and yield on idle balances.
Merchant Wallets, Provisioned at Signup
What: Every merchant gets a non-custodial Stellar account at signup. It sits behind the same email or phone login Nigerian SMEs already use, with no seed phrases, no wallet installs, and no crypto knowledge needed.
How Stellar is used: Privy generates a Stellar keypair via MPC (one key share held by Privy, one tied to the merchant's auth provider) and sets up the USDC trustline (changeTrust) when the account is created, so it can take payments from day one. That same Privy wallet later signs the SEP-10 challenge for Anchor and MoneyGram and the Soroban calls for Blend v2, so one account ends up being the identity layer for all six integrations.
Impact: A market trader in Lagos owns a real Stellar account the same day she opens her till. Collections, payouts, working capital, and yield all land in that one merchant-owned account, not in a custodial balance she can never take with her.
Digital Collections, Settled on Stellar
What: The collection rails Nigerian SMEs already use to get paid: POS terminals, online checkout, pay-with-transfer links, and dedicated virtual accounts.
How Stellar is used: Fiat collections can auto-convert from NGN to USDC on Stellar through the Anchor Platform deposit flow (SEP-24) if the merchant opts in, while crypto-funded payments route to USDC on Stellar via NEAR Intents. The settlement engine watches each merchant's account over Horizon's SSE streaming endpoint and credits the balance within seconds. Auto-convert is granular (per rail, by percentage, by cadence), and merchants who prefer Naira keep their NGN balance untouched.
Impact: This makes digital payments the default for Nigerian SMEs, and it turns every opted-in collection into an on-chain USDC settlement: recurring volume on Stellar that today just sits in fragmented PSP float. Stellar's sub-cent fees keep it viable even on small tickets like ₦500 airtime or ₦2,000 groceries.
Sweeping Cross-Chain Holdings into USDC
What: A crypto-funded merchant might receive ETH, BTC, USDT, or USDC across several chains. Instead of a separate position to track on each one, they see a single balance.
How Stellar is used: Each crypto inflow goes to the NEAR Intents 1Click API, where competing solvers settle it atomically and deliver native USDC straight to the merchant's Stellar account. There is no bridge, no wrapped asset, and no per-chain float for Paycashless to hold.
Impact: Scattered holdings collapse into one spendable USDC balance, and the risk of losing funds to the wrong chain goes away. Crypto-funded payments already make up $200K+ of Paycashless volume, and that value now lands on Stellar instead of sitting idle as USDT on Tron or USDC on Ethereum.
Naira Ramp & GLOBAL Payouts
What: Merchants pay staff and suppliers, in Nigeria and abroad, banked or unbanked, from the same account where their money lands.
How Stellar is used: Domestic naira payouts run on Paycashless's existing licensed MFB bank rail (NIBSS through Safe Haven MFB), unchanged. Stellar handles the cross-border leg through MoneyGram Access, a regulated anchor: a merchant authenticates with their Privy wallet (SEP-10, after SEP-1 discovery) and pays across borders through SEP-24 (interactive) or SEP-31 (programmatic, for recurring staff and supplier runs). A cross-border payout is funded from the merchant's USDC-on-Stellar balance, or from NGN converted to USDC at the quoted rate at payout time. When a recipient needs cash, USDC is swapped to XLM through a Stellar DEX pathPaymentStrictSend and handed to MoneyGram for pickup at 350K+ locations across 200+ countries. MoneyGram also serves as a regulated fallback if the domestic rail is down.
Impact: One integration reaches both banked and unbanked recipients and extends payouts beyond Nigeria with no per-country banking integration, while domestic naira keeps running on the licensed rail. Every cross-border payout leaves real activity on-chain: a USDC settlement, and for cash a USDC-to-XLM DEX trade plus a Stellar payment.
Yield on Idle Balances
What: An opt-in Earn feature, off by default, that puts idle merchant USDC to work instead of leaving it dormant between collections and payouts.
How Stellar is used: Once a merchant turns Earn on and sets a liquid reserve threshold (say, "always keep $200 liquid"), the settlement engine supplies the excess USDC into Blend v2 pools on Soroban via supply(), and tops the balance back up with withdraw() before any payout or off-ramp. The dashboard reads the yield straight from the contract in real time.
Impact: Idle treasury starts earning instead of sitting still, with no custodial counterparty in the middle. It also brings real TVL to Soroban from an audience that would never otherwise touch DeFi: Nigerian SMEs trying to hold their purchasing power against Naira inflation.
Requested Budget
$78.0K
Traction Evidence
Traction
Launched January 2026
$600K+
 in total merchant transaction volume processed since launch
$200K+
 of that volume originating from crypto-funded payments (
Arkham
)
Live merchants across fashion retail, restaurants, and finance verticals in Nigeria. Notable mentions are
Sky Hostel (
skyhostel.ng
), student accommodation, Nigeria
Venco (
venco.africa
), residential community management platform
Catlog (
catlog.shop
), commerce platform for online sellers
Woodcore (
woodcore.co
), core banking software
FrontEdge (
frontedge.io
), trade finance for exporters
Gidiburger (
gidiburger.com
), restaurant group, Lagos
Sufpay (
sufpay.ng
), government payments processor
Tranche 1 (Deliverable Roadmap) - MVP
Tranche 1 - MVP 
Total Budget:
 $23,400
Estimated Timeline:
 6 weeks after kickoff
T1 is the first tranche, released on approval. It stands up the integration environments and technical design, provisions a Stellar account for every merchant, and shows the first crypto-funded collection consolidating to USDC on the Stellar testnet end to end.
Deliverable 1: Integration Environments & Technical Design
Description:
 Provision the testnet environments and partner access for every integration and lock the technical design before the build proper begins.
Key Tasks:
Testnet access and credentials for Privy and NEAR Intents
Repository, CI, and deployment scaffolding, with environment configuration and secrets management
Success Metrics:
 Testnet environments live for the integrations with access confirmed, and the architecture reference plus repository scaffolding published for review.
Timeline:
 Completion by Week 2.
Budget:
 $7,800
Deliverable 2: Stellar Settlement Engine & Embedded Wallets
Description:
 The foundation every other module builds on. It provisions a Stellar account for every Paycashless merchant, existing and new, and stands up the Settlement Engine that tracks the USDC-on-Stellar balance.
Key Tasks:
Privy SDK plus the Paycashless auth path, so every merchant gets a Stellar account no matter how they log in
Stellar account provisioning surfaced natively in the merchant dashboard, with the USDC trustline set via changeTrust at account creation
Settlement engine using Horizon SSE streaming for real-time balance updates, reconciling Stellar tx hashes to Paycashless ledger entries
Migration that auto-provisions a Stellar account for existing merchants on their next login
Success Metrics:
 A testnet demo where a merchant signs in, a Stellar account with a USDC trustline appears in their dashboard, and a test USDC payment is credited to that account, visible on Stellar Expert with published transaction hashes. Demo recording published.
Timeline:
 Completion by Week 4.
Budget:
 $9,600
Deliverable 3: First Crypto-Funded Collection Consolidating to USDC (NEAR Intents)
Description:
 Wire the first crypto-funded collection path so an inbound payment on an external chain consolidates to USDC on the merchant's Stellar account, proving the consolidation model end to end on testnet.
Key Tasks:
Integrate the NEAR Intents 1Click API with an intent-creation service and solver callback handlers
Detect an inbound crypto payment and submit an intent that delivers native USDC to the merchant's Stellar account
Settlement confirmation and real-time balance display via Horizon streaming
Timeout and fallback handling when no solver fills
Success Metrics:
 A recorded testnet demo where a simulated ETH payment (and a USDT-on-Tron payment) triggers a NEAR Intent that settles as USDC in the merchant's Stellar account, visible on Stellar Expert with published transaction hashes.
Timeline:
 Completion by Week 6.
Budget:
 $6,000
Deliverable budgets sum to the $23,400 Tranche 1 total.
Tranche 2 (Deliverable Roadmap) - Testnet
Tranche 2 - Testnet
Total Budget:
 $26,400
Estimated Timeline:
 16 weeks after kickoff
T2 brings the full consolidation, cross-border payout, and yield feature set live on testnet, then validates it with a closed beta of active Paycashless merchants.
Deliverable 1: Full Multi-Chain NEAR Intents Consolidation
Description:
 Extend crypto consolidation from the single T1 path to the full set of supported source chains, so every crypto-funded inflow lands as USDC on the merchant's Stellar account regardless of origin chain.
Key Tasks:
NEAR Intents consolidation across the supported source chains (Ethereum, Tron, Bitcoin, and others), each delivering native USDC to Stellar
Per-chain monitoring, intent creation, and reconciliation tied to Stellar tx references
Slippage floors, retries, and circuit-breaker handling per chain
Success Metrics:
 A testnet demo where inbound crypto from at least three source chains all settles into one USDC-on-Stellar balance for a single merchant, each leg visible on Stellar Expert and the relevant source explorer.
Timeline:
 Completion by Week 10.
Budget:
 $6,400
Deliverable 2: Cross-Border & Cash Payouts (MoneyGram Access on the Anchor Platform)
Description:
 Let merchants pay suppliers and contractors outside Nigeria, and pay unbanked recipients in cash, through MoneyGram Access as the regulated anchor. A cross-border payout is funded from the merchant's Stellar USDC balance, or from NGN converted to USDC at the quoted rate at payout time. Domestic NGN payouts continue on the existing bank rail.
Key Tasks:
Anchor discovery (SEP-1), authentication (SEP-10), interactive withdrawal (SEP-24), and programmatic cross-border payouts (SEP-31) against MoneyGram Access on testnet
NGN-to-USDC conversion at payout time (a Paycashless treasury operation, not a Stellar-anchor on-ramp) for merchants funding a cross-border payment from their naira balance
MoneyGram cash pickup via a Stellar DEX pathPaymentStrictSend (USDC to XLM), with reference-number and status tracking
Payout UI for choosing bank rail versus cash pickup, with status surfaced in the dashboard, and MoneyGram available as a fallback if the domestic rail is down
Success Metrics:
 A testnet demo showing a programmatic SEP-31 cross-border payout and a MoneyGram cash-out that converts USDC to XLM on the Stellar DEX and returns a MoneyGram reference number, all with Stellar Expert references published.
Timeline:
 Completion by Week 13.
Budget:
 $10,000
Deliverable 3: Blend v2 Treasury Yield & On-Chain BI Pipeline
Description:
 Ship opt-in yield on idle USDC via Blend v2 (supply side only) and stand up the on-chain BI pipeline that reads settlement history for analytics and reporting.
Key Tasks:
Provision Blend v2 testnet access and wire the Soroban supply-side integration
Blend v2 opt-in Earn: a merchant enables it, sets a liquid reserve, and excess USDC is supplied via supply, withdrawn on demand via withdraw, with claim for rewards
BI pipeline consuming the Settlement Engine event bus plus Horizon SSE, producing real-time analytics and reporting
Success Metrics:
 A testnet demo where a merchant enables Earn, excess USDC is supplied to Blend and later withdrawn, with the Blend contract IDs and transaction references published on Stellar Expert, and a dashboard showing live on-chain analytics.
Timeline:
 Completion by Week 14.
Budget:
 $6,000
Deliverable 4: Unified Merchant Dashboard & Closed Beta
Description:
 Bring the NGN bank balance and the USDC-on-Stellar balance into one merchant view, and run a closed beta with active Paycashless merchants on the testnet rails.
Key Tasks:
Unified dashboard surfacing both balances, collections, payouts, and settlement status in real time over Horizon SSE and the bank-rail feed
Closed beta with at least 10 active Paycashless merchants across the fashion retail, restaurant, and finance verticals
Bug capture, prioritization, and fixes from beta feedback
Success Metrics:
 A dashboard at a public URL showing the testnet integrations working with live data across both balances, plus a beta report covering the participating merchants, issues found, and how they were resolved.
Timeline:
 Completion by Week 16.
Budget:
 $4,000
Deliverable budgets sum to the $26,400 Tranche 2 total.
Tranche 3 (Deliverable Roadmap) - Mainnet
Tranche 3 - Mainnet (Released Upon Mainnet Launch)
Total Budget:
 $28,200
Estimated Timeline:
 24 weeks after kickoff
T3 takes the full stack to mainnet, hardens the crypto-consolidation rail for production, and validates the integrations with real merchants through a professional user-testing program. It is released on mainnet launch.
Deliverable 1: Mainnet Deployment & End-to-End Testing
Description:
 Move every integration from testnet to live Stellar mainnet, with rigorous edge-case and error-handling validation across the full lifecycle.
Key Tasks:
Deploy the Settlement Engine, Privy wallets, NEAR Intents consolidation, MoneyGram Access on the Anchor Platform (SEP-24/SEP-31), and Blend v2 Earn to mainnet
Test error handling, retries, and edge cases across consolidation, cross-border payout, cash pickup, and yield
Publish a deployment runbook with contract IDs and an endpoint inventory
Success Metrics:
 All integrations live on Stellar mainnet, with at least 3 real transactions per flow verified on Stellar Expert and a deployment report published.
Timeline:
 Completion by Week 22.
Budget:
 $8,200
Deliverable 2: NEAR Intents Production Hardening & Additional Source Chains
Description:
 Harden the crypto-consolidation rail for production so it stays reliable under real traffic: add a treasury backstop so an intent no solver fills still consolidates, extend coverage to additional source chains, and set per-corridor reliability targets.
Key Tasks:
Treasury backstop: when no solver fills an intent within the window, Paycashless fills it from its own USDC so the merchant's balance still consolidates to USDC on Stellar instead of stranding funds on the source chain
Extend NEAR Intents source-chain coverage beyond the initial Ethereum, Tron, and Bitcoin set
Per-corridor fill-rate, latency, and slippage targets, wired into the production monitoring stack
Success Metrics:
 On mainnet, an intent left unfilled by solvers is consolidated through the treasury backstop within the stated SLA, and at least one additional source chain is live, each with references published on Stellar Expert.
Timeline:
 Completion by Week 21.
Budget:
 $4,000
Deliverable 3: Professional User Testing & Real-World Validation
Description:
 Run a professional user-testing program with real merchants on the production stack, and capture signed validation of the full flow.
Key Tasks:
Structured usability and user-testing sessions with active merchants across the fashion retail, restaurant, and finance verticals
Document the testing program, findings, and fixes, and collect signed confirmations
Success Metrics:
 A published user-testing report covering the sessions, findings, and resolutions.
Timeline:
 Completion by Week 23.
Budget:
 $10,000
Deliverable 4: Public Documentation, Developer Guide & Reference Repo
Description:
 Publish complete documentation plus a public reference repository for the Stellar integrations.
Key Tasks:
Update  docs site (
docs.paycashless.com
) covering settlement, consolidation, payouts, and yield
Publish a dedicated public reference repository with example implementations of the Anchor Platform, NEAR Intents, and Blend integrations, while the proprietary backend stays private
An integration walkthrough based on the merchant onboarding experience
Success Metrics:
 A publicly accessible documentation URL and a public reference repository with code examples for the Anchor, NEAR Intents, and Blend integrations.
Timeline:
 Completion by Week 23.
Budget:
 $3,000
Deliverable 5: Production Monitoring & Alerting
Description:
 Deploy monitoring and alerting so the integrations stay reliable under real merchant traffic.
Key Tasks:
Monitoring across consolidation, cross-border payouts, MoneyGram cash-outs, Blend supply/withdraw, and overall integration health and error rates
Automated alerting for failed settlements or anomalous activity
A published uptime SLO
Success Metrics:
 A live monitoring dashboard showing the real-time health of all integrations, with tested alerting rules and an uptime target of 99.5% or higher.
Timeline:
 Completion by Week 24.
Budget:
 $3,000
Deliverable budgets sum to the $28,200 Tranche 3 total.
Team
Ahmed Umar
, Cofounder Products and payments leader in Africa. Previously built at Risevest (Techstars '20) and Moniepoint (unicorn), shipped crypto infrastructure at Onramper, and worked on open banking across EMEA with Mastercard and Mono (YC W21). Hands-on builder on Stellar with deep blockchain and payments infrastructure experience. LinkedIn:
https://www.linkedin.com/in/ahmedtijaniumar/
Umar Abdullahi
, Cofounder Serial entrepreneur and engineering leader in Africa. Previously built at Fincra, processing 5% of all remittances to West Africa and establishing payment partnerships with PayPal. Worked on open banking across EMEA with Mastercard and Mono (YC W21), and tackled food delivery in northern Nigeria with Grubways. Brings deep engineering experience across blockchain and payments infrastructure. LinkedIn:
https://www.linkedin.com/in/umar-abdullahi-789a2b130/
Umar Abdullahi
Umar
```
