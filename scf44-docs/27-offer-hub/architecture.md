Source: https://www.offer-hub.tech/architecture

(Text copy of the web page. The page diagrams are drawn by JavaScript and are not included.)

SCF Build Award #44 — Integration Track
Technical Architecture
Complete system design for a non-custodial freelance marketplace on Stellar — from client-side Soroban signing to fiat settlement across 7 LATAM markets.
Client
Next.js 15 + SWK
API
NestJS + Prisma
Stellar
Soroban + USDC
Non-custodial · On-chain · LATAM-native
Overview
System
Payment Flow
Integrations
Roadmap
Why Stellar
Traction
Architecture Overview
OfferHub System Architecture
A comprehensive view of the OfferHub platform, showing the flow between our modern frontend, NestJS orchestrator, and external integrations.
This flowchart maps every layer of the OfferHub stack — from the browser and Stellar Wallets Kit on the client side, through the NestJS orchestration API and PostgreSQL/Redis data layer, down to Soroban smart contracts on Stellar and the BlindPay off-ramp provider. Arrows show the protocol or method used at each boundary. Teal nodes are the two SCF Integration Track building blocks.
For a better view, click
to expand
SCF Integration / Client
Internal API
Persistence / Chain
Payment Flow Lifecycle
Escrow Lifecycle & Payouts
A secure, non-custodial workflow powered by Soroban smart contracts and executed via Stellar Wallets Kit.
The sequence diagram traces a complete escrow lifecycle: from order creation to fiat settlement. All Soroban transactions (fund_escrow and release_escrow) are signed client-side by the user's wallet via Stellar Wallets Kit — no private keys ever touch the server. The state diagram on the right shows every valid order state, including the dispute resolution path.
Transaction Flow
For a better view, click
to expand
Escrow State Machine
For a better view, click
to expand
Ecosystem Integrations
Hub and Spoke Architecture
OfferHub acts as the orchestrator, integrating best-in-class solutions for wallet management, escrow, and global fiat off-ramps.
OfferHub uses two building blocks from the official SCF Integration List. Stellar Wallets Kit handles non-custodial wallet connection and client-side Soroban signing. BlindPay routes USDC to bank accounts across 7 LATAM corridors via SPEI, Pix, PSE, and Transfer 3.0. The NestJS orchestrator selects the corridor automatically based on freelancer country and payout preference.
OfferHub
NestJS Orchestrator
Routes USDC based on freelancer country
Stellar Wallets Kit
SCF Integration #1
Non-custodial signing
Freighter
Lobstr
xBull
• create_escrow
• release_escrow
• refund_escrow
• resolve_dispute
BlindPay
SCF Integration #2
FinCEN MSB
YC-backed
MX/SPEI
BR/Pix
CO/PSE
AR/Transfer 3.0
PE
CL
CR
2 SCF Integrations
•
7 BlindPay Corridors
Delivery Schedule
SCF Build #44 Roadmap
Detailed timeline for the execution of our $74,000 SCF Build Award milestones.
Three tranches, each tied to verifiable on-chain or functional deliverables. Tranche 1 ships the wallet connection layer. Tranche 2 completes both SCF integrations on testnet. Tranche 3 migrates to Stellar RPC, launches on mainnet, records 10 live transactions as proof, and open-sources the integration adapters.
For a better view, click
to expand
Milestone
Date
Technical Cost
SCF Payout
%
T0 — Upfront
On award
—
$8,000
10%
T1 — MVP: SWK Connection & Auth
Sept 1, 2026
$16,000
$16,000
22%
T2 — Testnet: Core Integrations
Oct 20, 2026
$19,500
$19,500
26%
T3 — Mainnet Launch & OS Adapters
Dec 5, 2026
$30,500
$30,500
41%
Total
$74,000
$74,000
100%
Why Stellar
The case for Stellar
What Stellar uniquely enables for OfferHub that no other chain or payment rail provides.
This section answers the SCF panel's core question: why is Stellar the right foundation for OfferHub, and what value does this integration bring to the Stellar ecosystem?
USDC on Stellar
Stellar's native USDC support means freelancers receive payment in a stable, internationally liquid asset — no wrapping, no bridge risk, no slippage. Every escrow is denominated in USDC, eliminating currency volatility from the payment contract.
Sub-second finality, ~$0.0001/tx
Stellar confirms transactions in 3–5 seconds with fees under a fraction of a cent. For a marketplace processing hundreds of escrow releases weekly, this makes on-chain payment settlement economically viable at any order size — including micro-orders under $10.
Soroban smart contracts (TrustlessWork)
Soroban enables programmable, audited escrow logic on Stellar. OfferHub integrates TrustlessWork's open-source Soroban contracts rather than building from scratch — leveraging already-audited code and contributing to Stellar's composable DeFi layer.
Non-custodial by design
Stellar Wallets Kit allows client-side transaction signing. Funds never pass through OfferHub's servers. This is the correct model for a marketplace handling freelancer payments at scale — OfferHub cannot freeze, redirect, or lose user funds.
LATAM off-ramp coverage
BlindPay covers 7 payout corridors across 6 LATAM countries — all settled from on-chain USDC. This makes Stellar the settlement layer for a population where 47% of the workforce is unbanked and traditional payment rails are unreliable or inaccessible.
Ecosystem value OfferHub adds
OfferHub brings real freelance transaction volume to Stellar mainnet. Every completed order is an on-chain USDC escrow release followed by a fiat settlement via a Stellar anchor. Open-sourcing the SWK + BlindPay adapters in T3 creates reusable building blocks for any future Stellar marketplace.
Platform Traction
Live on testnet. Built for scale.
Current platform metrics and the validated LATAM freelance market opportunity.
The SCF Integration Track requires verifiable traction. This section shows OfferHub's current testnet status, team readiness, and the market context that validates the integration investment.
Platform status
Testnet live
NestJS API
12 modules in production
PostgreSQL
Orders, escrow, reviews, payments
GitHub
Open-source codebase
Next.js 15
Production frontend deployed
Build readiness
Full-stack TypeScript team with NestJS + Next.js experience
Existing Stellar integration (custodial keypairs → migrating to non-custodial via SWK)
TrustlessWork integration contracts already tested on testnet
BlindPay onboarding initiated — estimated 1–2 weeks to production
The LATAM freelance gap
$85B
LATAM freelance market size (2024)
47%
Workforce without bank accounts — Colombia
7
Countries covered by BlindPay corridors
Traditional platforms (Upwork, Fiverr) cannot pay unbanked LATAM freelancers directly. OfferHub + Stellar + BlindPay closes this gap: any freelancer with a mobile wallet receives USDC-settled payments in seconds, converted to local fiat via established licensed corridors.


## Diagram sources (Mermaid)

The page diagrams are drawn from these Mermaid sources in the site's repo: https://github.com/OFFER-HUB/offer-hub-monorepo/tree/HEAD/src/components/architecture

### system-architecture.charts.ts

```ts
/** Mermaid chart sources for SystemArchitectureDiagram (extracted). */
export const SYSTEM_ARCHITECTURE_CHART = `
flowchart TD
    %% Node Styling
    classDef highlight fill:#149A9B,color:#fff,stroke:#0d7377,stroke-width:2px;
    classDef backend fill:#002333,color:#fff,stroke:#001522,stroke-width:2px;
    classDef subtle fill:#F1F3F7,color:#19213D,stroke:#d1d5db,stroke-width:1px;

    Client["Client Layer<br/><small>Next.js 15 · React 19 · Zustand · NextAuth v5</small>"]:::highlight
    Wallet["Wallet Layer<br/><small>Stellar Wallets Kit · Freighter · Lobstr · xBull</small>"]:::highlight
    API["NestJS API<br/><small>Auth · Orders · Escrow · Payments · Webhooks · Off-ramp</small>"]:::backend
    Data["Data Layer<br/><small>PostgreSQL · Redis · BullMQ</small>"]:::subtle
    Stellar["Stellar<br/><small>Soroban Contracts · TrustlessWork · USDC</small>"]:::subtle
    Offramp["Off-ramp<br/><small>BlindPay (7 corridors)</small>"]:::highlight

    Client -->|HTTPS / REST| API
    Client -->|Client-side Soroban signing| Wallet
    API -->|Prisma ORM| Data
    API -->|Stellar RPC| Stellar
    Stellar -->|Webhook / on-chain event| API
    API -->|API / Webhooks| Offramp
    Wallet -.->|Sign & Submit| Stellar
  `;
```

### payment-flow.charts.ts

```ts
/** Mermaid chart sources for PaymentFlowDiagram (extracted). */
export const PAYMENT_SEQUENCE_CHART = `
sequenceDiagram
    participant Client as Client (buyer)
    participant SWK as SWK
    participant NestJS as NestJS (OfferHub API)
    participant TW as TrustlessWork (escrow)
    participant Offramp as BlindPay

    Client->>NestJS: POST /orders (USDC reserved)
    NestJS->>TW: deployEscrow(buyer, seller, amount)
    TW-->>NestJS: escrowId + contractAddress
    NestJS-->>Client: { escrowId, contractAddress }
    
    Client->>SWK: signTransaction(fund_escrow)
    SWK->>TW: fund_escrow (USDC on-chain)
    
    Note over TW: State: ESCROW_FUNDED → IN_PROGRESS
    
    Client->>SWK: signTransaction(release_escrow)
    SWK->>TW: release_escrow
    TW-->>NestJS: webhook: escrow_released
    NestJS->>Offramp: POST /payout (USDC → fiat)
    Offramp-->>Client: fiat settled (SPEI / Pix / PSE...)
  `;

export const PAYMENT_STATE_CHART = `
stateDiagram-v2
    [*] --> CREATED
    CREATED --> RESERVED
    RESERVED --> ESCROW_FUNDED
    ESCROW_FUNDED --> IN_PROGRESS
    IN_PROGRESS --> COMPLETED
    IN_PROGRESS --> DISPUTED
    DISPUTED --> RESOLVED
    COMPLETED --> [*]
    RESOLVED --> [*]
  `;
```

### scf-tranche-roadmap.charts.ts

```ts
/** Mermaid chart sources for SCFTrancheRoadmap (extracted). */
export const SCF_GANTT_CHART = `
gantt
  title OfferHub SCF Build #44 — Delivery Roadmap
  dateFormat YYYY-MM-DD
  axisFormat %b %d

  section T1 — MVP: SWK Connection & Auth ($16K)
    SWK Wallet Connection + Balance Display    :t1a, 2026-07-01, 2026-09-01
    Wallet-Based Auth (hybrid)                 :t1b, 2026-08-01, 2026-09-01

  section T2 — Testnet: Core Integrations ($19.5K)
    Soroban Client-Side Signing (SWK)          :t2a, 2026-09-01, 2026-10-20
    BlindPay — 7 LATAM Corridors               :t2b, 2026-09-01, 2026-10-20
    Off-ramp Orchestration + Webhooks          :t2d, 2026-09-15, 2026-10-20
    E2E Integration Testing                    :t2e, 2026-10-01, 2026-10-20

  section T3 — Mainnet Launch & OS Adapters ($30.5K)
    Horizon → Stellar RPC Migration            :t3a, 2026-10-20, 2026-12-05
    Mainnet Launch + Monitoring                :t3b, 2026-10-20, 2026-12-05
    Open-Source Integration Adapters           :t3c, 2026-11-15, 2026-12-05
  `;
```
