Source: https://ivory-tory-46.tiiny.site (PDF: https://ivory-tory-46.tiiny.site/Azza-Stellar-SCF-Technical-Proposal_adjusted_feedback.pdf)

# Azza x Stellar: USDC Savings & Global Payout Rails on Stellar

Transcribed from the 16-page PDF "Azza-Stellar-SCF-Technical-Proposal_adjusted_feedback" (Technical Spec, June 2026, 18 weeks, 3 tranches, integrations: Blend v2 and Bridge). The PDF states a total build award request of $95,000; the published SCF #44 submission states $89.0K. The original PDF is saved next to this file as `architecture.pdf`.

## A. Product integration scope

Azza is a live WhatsApp-based stablecoin payments app. The proposal adds two Stellar-native capabilities, reusing Azza's existing custody, KYC and payout infrastructure.

- Feature 1, Yield: **Azza Savings**, WhatsApp-native USDC savings powered by Blend v2. Azza is the custodian and accounting layer; Blend v2 on Soroban is the yield engine.
- Feature 2, Settlement: **Global Payout Rails**, a new provider in Azza's payout orchestration network that uses Bridge, settling over Stellar, to extend on/off-ramp into USD, EUR and GBP.

Who uses each: Savings serves consumers in Nigeria, Kenya, Ghana and South Africa who already hold USDC via Azza. Payout rails serve users and businesses sending or receiving across borders (for example an African freelancer paid in USD).

Savings user journey:
1. User tells Azza via WhatsApp: `Save $50`.
2. Azza moves that USDC from the user's Azza balance to its Stellar treasury account and deposits it into a Blend v2 lending pool.
3. User can see savings balance and current APY in WhatsApp.
4. Yield accrues continuously and is reflected in the savings balance.
5. On `Withdraw my savings`, Azza redeems from Blend v2; the user keeps USDC or off-ramps to local bank or mobile money.

Headline figures: 5-8% target USDC APY via Blend v2; 3+ new international corridors via Bridge (USD, EUR, GBP); 12 fiat corridors live today.

## B. Selected Stellar integrations

**Blend v2 (primary, yield).** Stellar's native lending protocol on Soroban. Azza deposits pooled user USDC into a Blend v2 USDC lending pool via Soroban contract calls; users never interact with Blend directly. Integration time 5-6 weeks. Dependencies: Blend v2 mainnet contract addresses, SDF/Blend team for ABI and liquidity-depth confirmation, Soroban RPC endpoint.

**Bridge (primary, settlement).** Stablecoin payments and treasury platform (Stripe-acquired) on the Stellar Integration List. Its Orchestration API accepts a payout instruction and handles FX, routing and settlement using Stellar, delivering to bank rails (ACH/SEPA/SWIFT) or stablecoin wallets. Bridge joins Azza's existing Offramp Provider Router as a new provider for international corridors; on onramp it converts inbound USD/EUR/GBP into USDC. Integration time 4-5 weeks. Dependencies: Bridge API credentials and business onboarding, corridor and settlement-asset confirmation, Stellar settlement addresses.

**Stellar SDK + Horizon (supporting).** Manages Azza's Stellar treasury accounts, submits transactions to Blend v2, streams payment events via Horizon SSE, confirms Bridge settlement transactions. Integration time 3-4 weeks.

Stellar-specific considerations:

| Consideration | How it's handled |
|---|---|
| Transaction and fee model | Wallet Service simulates each Soroban call (simulate-then-submit) to get resource footprint and fee before signing. |
| Asset handling and trustlines | Treasury USDC trustline set up once in Tranche 1. |
| Treasury signer model | Single-signer treasury in Tranches 1-3 from the existing secrets manager; multi-sig deferred post-grant. |
| Soroban failure modes | Contract panics and resource exhaustion mapped to error classes in the existing retry framework (retry or dead-letter). |
| Finality and monitoring | Deposit confirmed once its ledger closes (~5 s); Horizon metrics and Stellar Expert for observability. |

Team readiness: Tranche 1 absorbs the Rust/Soroban learning curve on testnet; mainnet deferred to Tranche 3. Before Tranche 2 the team completes Soroban quickstart and a testnet deposit/withdraw cycle.

## C. Proposed architecture

Current system (in production): WhatsApp users -> WhatsApp Business API -> WhatsApp Bot Service (conversational layer + AI agent (Claude), sessions) -> FiatRamp Service, CrossBorder Service (NGN->GHS, NGN->ZAR), QR Payments Service -> BullMQ queue layer (fiatPayoutV3, crossBorderPayout, onramp) -> AzzaWallet Manager (Blockradar; EVM chains plus Solana, Tron), Offramp Transaction Manager (Provider Router: SafeHaven, YellowCard, Bell, PalmPay, Redbiller, HoneyCoin, Payd, Manteca), User/KYC Identity Service -> PostgreSQL (Drizzle ORM).

Supported fiat corridors: NGN, KES, ZAR, GHS, ARS, BRL, PEN, BOB, USD, EUR, GBP.

Custody model: each user has a dedicated on-chain wallet (via Blockradar); Azza authorizes debits via API-level control. The Azza hot wallet holds crypto debited during offramp.

New Stellar savings layer (all additive):
- WhatsApp bot: new savings flows (deposit, balance query, withdrawal).
- **Azza Savings Service (new):** checks KYC and tier, reads/writes StellarSavingsAccount, coordinates with BlendV2IntegrationService, triggers offramp via the existing OfframpPayoutTransactionManager.
- **Stellar Wallet Service (new):** treasury account(s), key management via secrets manager, signs and submits via Stellar SDK.
- **Yield Accounting Service (new):** tracks each user's share of the pooled Blend position, computes accrued yield, writes `stellar_yield_history`, runs hourly.
- **Stellar Horizon Listener (new):** streams payment events to treasury accounts, confirms USDC deposits before Blend submission, reconnect and retry.
- **Blend v2 (Soroban):** USDC lending pool on mainnet; Azza holds one pooled lender position; `deposit_collateral()` / `withdraw()`.

Global Payout Rails: User/business (WhatsApp or API) -> Offramp/Onramp Transaction Manager -> Provider Router -> African corridor providers (unchanged) or **Bridge Provider Adapter (new)** -> Bridge Orchestration API (FX, routing, compliance) -> Stellar settlement (USDC/EURc, Horizon-confirmed) -> destination bank rail or stablecoin wallet. The router logic is unchanged; the new code is the Bridge adapter and Stellar settlement reconciliation.

Product flows:
- Savings deposit: validate KYC and balance -> debit internal ledger -> transfer USDC to treasury -> Horizon confirms -> `deposit_collateral()` -> record pool share.
- Savings withdrawal: read balance and accrued yield -> `withdraw()` on Blend -> USDC back to treasury -> credit user -> existing offramp to bank.
- International payout: manager marks corridor international -> router picks Bridge -> adapter debits USDC and submits instruction -> Bridge converts, screens, settles via Stellar; Horizon confirms -> fiat delivered (for example SEPA); adapter reconciles webhook with Stellar settlement.

Wallet handling (custodial): shared treasury model, not per-user Stellar keypairs. Per-user shares are tracked in `stellar_savings_accounts`, because Blend sees Azza as a single lender position.

Monitoring, compliance and failure handling:

| Concern | Approach |
|---|---|
| Blend v2 utilisation spike | Soroban view call on a cron; auto-suspend new deposits above threshold. |
| Horizon stream disconnect | SSE reconnect with exponential backoff; no Blend deposit until the Horizon event arrives. |
| Soroban transaction failure | Retry with idempotency key from internal deposit ID; dead-letter after 3 failures. |
| Over-withdrawal | Enforced in the Savings Service before any Soroban call. |
| KYC gating | Deposits require `kycStatus === 'verified'`. |
| Yield accounting drift | Daily job: sum of user shares must equal on-chain Blend position. |
| Bridge settlement mismatch | Payout complete only when Stellar tx and webhook both confirm. |
| Bridge webhook authenticity | Signature verification; idempotency keys. |
| Provider failover | Router falls back; international corridors with no fallback are queued. |
| Regulatory | User-initiated deposits only, withdrawal always available; Bridge compliance for payouts; legal review recommended for Nigeria/Kenya before mainnet. |

## D. Milestones (as stated in the PDF)

Tranche 0 (10%, $9,500) on approval as kickoff advance. Success metric: by end of Tranche 3, route 21-29% of total transaction volume through Stellar, ramping to ~41% steady state.

- **Tranche 1, weeks 1-6, $19,000:** Stellar treasury + testnet USDC transfers ($4,500); BlendV2IntegrationService deposit/withdraw on testnet ($6,000); Bridge sandbox integration ($5,000); staging environment ($3,500).
- **Tranche 2, weeks 7-13, $28,500:** WhatsApp savings deposit + balance/APY ($6,000); savings withdrawal to local fiat ($5,000); Bridge in Provider Router for USD/EUR/GBP ($7,000); international onramp via Bridge ($3,500); Yield Accounting Service ($4,500); risk monitoring ($2,500).
- **Tranche 3, weeks 14-18, $38,000:** Blend v2 savings on mainnet (Nigeria, Kenya) ($8,000); Bridge payout rails on mainnet ($8,500); beta cohort operations ($2,000); referral campaign ($2,500); capped APY boost for first 250 depositors ($1,500); full QA ($7,000); production observability ($5,000); documentation, SCF reporting and handover ($3,500).

## E. Budget (as stated in the PDF, $95,000)

Blend v2 backend integration $24,000; Bridge backend integration $22,000; provider orchestration and payout routing $9,000; wallet/asset and treasury management $9,000; WhatsApp UX $8,000; QA/testing $11,000; infrastructure/observability $7,000; PM/engineering coordination $5,000.

## Application narrative (summary)

At full run-rate Azza projects ~$300,000/month Bridge-settled USD/GBP volume plus ~$80,000/month Blend v2 savings, about 41% of total volume routed through Stellar.
