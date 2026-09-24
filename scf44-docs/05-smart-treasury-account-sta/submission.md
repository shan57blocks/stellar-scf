# Smart Treasury Account (STA): Programmable Stellar Treasuries

Source: https://communityfund.stellar.org/submissions/recmj5cqlrqKyd1Bc (SCF #44, awarded $127.5K, category: Financial Protocols)

- Website: https://smarttreasury.io
- Architecture doc: https://github.com/Smart-Treasury-Account-STA/smart-contracts/blob/main/docs/TECHNICAL_ARCHITECTURE.md

## Links in the submission

- https://github.com/Smart-Treasury-Account-STA/smart-contracts/blob/main/docs/TECHNICAL_ARCHITECTURE.md
- https://github.com/Smart-Treasury-Account-STA/smart-contracts/tree/main
- https://github.com/Smart-Treasury-Account-STA/smart-contracts
- https://github.com/Smart-Treasury-Account-STA/smart-contracts/blob/main/docs/POC_REVIEW_GUIDE.md
- https://github.com/Smart-Treasury-Account-STA/smart-contracts/blob/main/docs/TESTNET_DEPLOYMENT.md

## Submission text (as published)

```text
Products & Services
Product
Smart Treasury Account (STA) is a programmable treasury wallet on Stellar that helps organizations manage payments, approvals, automation, and recovery through Soroban smart contracts. STA uses Stellar contract accounts, Stellar Asset Contracts, Stellar Wallets Kit, Freighter, xBull, and Stellar RPC simulation to provide secure, policy-controlled treasury operations.
Programmable Treasury Account:
 STA creates a Soroban contract account that can hold and manage approved Stellar Asset Contract balances. This improves the project by moving treasury control from manual wallet operations to enforceable onchain policy.
Wallet-Based Treasury Access:
 STA integrates Freighter and xBull through Stellar Wallets Kit for user connection and transaction approval. Freighter supports transaction and Soroban authorization-entry signing, while xBull is supported as a Wallets Kit module, with its exact signing path confirmed during implementation testing. This makes the product easier for Stellar users to adopt while keeping approvals tied to familiar wallet workflows.
Policy-Controlled Payments and Payouts:
 STA supports approved assets, recipients, signer roles, thresholds, limits, vendor payments, payroll, and revenue splits. Stellar is used through SAC transfers and Soroban authorization, improving treasury safety and transparency.
Scheduled and Conditional Automation:
 STA enables recurring or condition-based treasury actions through Soroban intent logic, relayer submission, and onchain validation. This improves the project by allowing automation without giving relayers custody or authority over funds.
Attestation-Based Execution (optional extension):
 STA can optionally require signed external proofs, such as invoice approval or delivery confirmation, before releasing payment. Stellar smart contracts verify these conditions onchain, connecting real-world business workflows to secure Stellar payments.
Recovery, Monitoring, and Auditability:
 STA includes pause, freeze, guardian recovery, event emission, and audit trails for signer changes, policy updates, payments, automation, and recovery. This improves operational resilience and gives teams clear visibility into treasury activity.
Use Cases
1. Programmable Stablecoin Treasury for Businesses
Smart Treasury Account (STA) enables companies, startups, nonprofits, and distributed teams to manage stablecoin treasury operations on Stellar, including vendor payments, payroll, contributor payments, recurring expenses, spending limits, signer approvals, and emergency recovery.
STA uses Soroban contract accounts and Stellar Asset Contracts to enforce treasury policies directly onchain. This gives organizations a secure, auditable way to manage Stellar assets without relying on manual wallet operations or a single signer.
2. Marketplace and Platform Payout Automation
STA enables marketplaces and digital platforms to automate payouts to sellers, freelancers, creators, service providers, affiliates, and partners. Platforms can configure payout rules, split revenue, require approvals, and trigger payments only when predefined conditions are met.
STA uses Stellar for efficient asset settlement and Soroban for programmable payout logic. Relayers can trigger scheduled or conditional payments without holding custody or authority over funds, making the system suitable for high-frequency payout workflows.
3. Tokenized Asset Issuer Treasury and Revenue Distribution
STA supports tokenized asset issuers, RWA platforms, funds, and protocol treasuries that need to distribute income, fees, redemptions, or proceeds under controlled policies.
STA uses Stellar Asset Contracts for asset movement and Soroban smart contracts for policy enforcement, recipient controls, spending limits, automation, audit logs, and recovery mechanisms. This provides issuers with treasury infrastructure for managing tokenized asset operations on Stellar.
Market fit
The market is moving toward stablecoin and tokenized-asset operations, but business treasury tooling is still immature. Stablecoins exceeded $230 billion in market capitalization in 2025, and the practical demand is shifting from holding assets to operating with them: paying contributors, settling vendors, splitting revenue, managing disbursements, and proving internal controls. STA targets this operational layer, where teams care less about speculation and more about permissions, limits, automation, recovery, and records.
The STA market-fit thesis is strong because it sits at the intersection of three growing needs: organizations want stablecoin payments, Stellar offers efficient settlement, and teams need treasury-grade policy before they can scale real operations. A small team may begin with one admin wallet, but a serious operator quickly needs multi-signer approvals, approved recipients, transfer limits, scheduled execution, emergency freeze, and recovery. STA converts that pain into a clear product surface; the thesis should be validated through design partners, testnet usage, and production payment volume.
Most treasury products are either offchain dashboards, generic multisigs, or EVM-first tooling. STA is different because it is designed specifically for Stellar and Soroban: SAC-first asset movement, contract-account authorization, policy-bound execution, non-custodial relayers, scheduled intent replay protection, and recovery controls. This makes it more aligned with Stellar's payment identity than a copied multisig pattern from another ecosystem.
The strongest early users are Stellar-native teams that already move funds but lack professional controls: grant programs, ecosystem projects, token issuers, payment apps, remittance operators, marketplaces, DeFi protocols, and organizations paying distributed contributors in stablecoins. These users feel the problem early because a single private key, spreadsheet approvals, or manual recurring payments become operationally risky once a team is handling recurring treasury activity or meaningful monthly payment volume.
Requested Budget
$127.5K
Traction Evidence
STA is led by Reto Grau, a financial markets professional with more than 30 years of experience in asset management and treasury operations, including roles at Man Group, JPMorgan, Swiss Life, and ABB Treasury.
The project also benefits from a partnership with Equisafe, a leading European tokenization platform that has facilitated over €400M in investments, supported 25,000+ private investors, and enabled 70+ fundraising rounds.
Early market validation is already visible: STA has received interest from 11 funds, RWA issuers, and Web3 companies, with six of them expressing strong interest in the product.
The opportunity on Stellar is driven by growing stablecoin adoption, tokenization activity, and payment volume. Stellar is well positioned for programmable treasury operations. Key use cases include treasury management, payroll, vendor payments, revenue sharing, DAO treasuries, and marketplace payouts.
Within 12 months of deployment, STA targets onboarding 30-50 organizations, deploying 300-500 Smart Treasury Accounts, processing 100,000-250,000 treasury operations on Stellar, and supporting $25M-$100M in treasury assets configured or managed through STA.
We have completed an initial Stellar/Soroban proof of concept for Smart Treasury Account and deployed the core PoC contracts on Stellar testnet.
This PoC is a partial implementation, not a completed product and not production-proven smart contracts. It is designed to validate the technical direction and demonstrate selected onchain patterns, including signer policy, treasury controls, policy validation, scheduled intent handling, replay protection, and recovery safeguards.
https://github.com/Smart-Treasury-Account-STA/smart-contracts
https://github.com/Smart-Treasury-Account-STA/smart-contracts/blob/main/docs/POC_REVIEW_GUIDE.md
The testnet deployment shows concrete progress that can be independently checked through documented contract IDs, deployment transactions, initialization transactions, and example interaction flows. The next phase is to complete the production contract suite, including full account authorization, real SAC transfer execution, adapters, relayer, SDK, dApp integration, monitoring, and broader integration testing.
https://github.com/Smart-Treasury-Account-STA/smart-contracts/blob/main/docs/TESTNET_DEPLOYMENT.md
Smart Treasury’s business model should follow an infrastructure-first approach. The core treasury account layer should prioritize adoption, trust, and Stellar ecosystem usage, with free testnet access and early ecosystem plans for teams validating treasury workflows.
Revenue can come from value-added services around the core protocol, including hosted dashboards, policy management tools, scheduled execution infrastructure, relayer services, monitoring, API access, activity reporting, and enterprise support.
The model should avoid AUM-based fees and heavy payment-volume fees in the early stage. Monetization should come from reliability, automation, convenience, and operational support, creating a sustainable business model without slowing ecosystem adoption.
The go-to-market wedge should be Stellar ecosystem treasury operations, not broad enterprise finance on day one. The first step is to complete and turn the current PoC into a testnet product demo with a clear user flow: create a treasury, add signers, approve assets, approve recipients, schedule payments, execute through a relayer, and view execution history. The next step is to onboard 5 to 10 design partners from Stellar projects, grant recipients, token issuers, payment apps, and ecosystem teams that already manage recurring payments or shared treasury operations. After the mainnet launch, Smart Treasury can publish workflow templates for common use cases such as monthly contributor payments, grant disbursements, revenue splits, stablecoin vendor payments, and operating expense controls.
The first adoption goal is trust, not volume. Smart Treasury should prove that teams can understand the signing flow, review policies before execution, test recovery safeguards, and clearly see what happened after each treasury action. Once these workflows are validated, the product can expand through SDK integrations, partner relayers, wallet integrations, and packaged treasury policies for Stellar apps.
The best ecosystem path is for Smart Treasury to become a trusted treasury module that Stellar teams can use before they manage meaningful funds.
Tranche 1 (Deliverable Roadmap) - MVP
Tranche 1 — MVP
Tranche duration: 1.5 months
Development budget allocation: $31,000
Deliverable 1: Smart Contract Development
Brief description:
 Build the MVP Soroban contracts for Smart Treasury Account. This includes signer management, signer weights, threshold authorization, replay protection, pause/freeze controls, approved asset rules, approved recipient rules, amount validation, SAC-based payment execution, and event emission. Signer authorization uses standard Ed25519 wallet signers no custom signature verifier is built. Admin/owner gating and pause/freeze state integrate OpenZeppelin’s audited Stellar Soroban Contracts library (stellar-access, stellar-contract-utils) instead of being implemented and security-reviewed from scratch in each contract module. What is uniquely new on-chain, beyond wallet approvals and signer-weight rules that resemble standard multisig math, is the PolicyEngine logic itself: asset, recipient, and amount rules pinned to a versioned policy state so that a later policy change cannot silently alter the semantics of payments already approved under an earlier version, plus nonce-based replay protection bound to that same policy version. This is the part of the system that does not exist as reusable Soroban infrastructure today.
Developer skills and allocation:
 1 Soroban/Rust smart contract developer as primary contributor, with limited backend support.
Estimated active duration:
 5 weeks
How to measure completion:
 The contracts compile and run locally. A developer can instantiate the contracts, configure signers and policies, execute an approved SAC-based payment, and confirm that invalid actions are rejected.
Budget: $21,000
Deliverable 2: Smart Contract Test Suite + Documentation
Brief description:
 Add Soroban unit and integration tests and full documentation for the MVP contracts. The tests cover signer rules, threshold checks, policy checks, SAC payment execution, replay protection, pause/freeze behavior, successful flows, and expected rejection flows. Tests focus on the guarantees that are unique to STA policy-version-mismatch rejection, nonce replay rejection, and signer-weight threshold edge cases rather than re-testing the imported access-control and pause primitives, which are already covered by OpenZeppelin’s upstream test suite and audit.
Developer skills and allocation:
 1 Soroban/Rust developer with part-time QA support.
Estimated active duration:
 4 weeks
How to measure completion:
 A developer can run the documented test command and see passing unit and integration tests for the MVP contract flows. A full technical documentation of the smart contracts and the tests, and deployment scripts on testnet are provided.
Budget: $10,000
Tranche 2 (Deliverable Roadmap) - Testnet
Tranche 2 — Testnet
Tranche duration: 1.5 months
Development budget allocation: $41,500
Deliverable 1: Testnet Smart Contracts Deployment
Brief description:
 Deploy the smart contracts to Stellar testnet and validate treasury setup, signer management, policy configuration, transaction simulation, and SAC-based payment execution. This is where the policy-version pinning and nonce replay protection from Tranche 1 are shown enforced live on a public ledger, not just basic wallet-approved transfers.
Developer skills and allocation:
 1 Soroban/Rust developer, with support from 1 backend developer for deployment and RPC flows.
Estimated active duration:
 2 weeks
How to measure completion:
 Testnet contract addresses are available. A developer can configure or inspect a testnet treasury account, execute an approved payment, and confirm that invalid actions are rejected.
Budget: $7,000
Deliverable 2: Testnet dApp and Wallet Flow
Brief description:
 Build the testnet dApp for treasury operators. This includes wallet connection, treasury dashboard, signer and policy screens, payment preparation, transaction simulation, wallet approval, transaction submission, status tracking. Wallet connectivity integrates Stellar Wallets Kit (Freighter, xBull) end to end; no custom wallet or signature-verification protocol is built, which reduces the backend support allocated here to RPC/simulation work only.
Developer skills and allocation:
 1 full-stack/frontend developer as primary contributor, with support from 1 Soroban/Rust developer and 1 backend developer.
Estimated active duration:
 5 weeks
How to measure completion:
 A user can open the testnet dApp, connect Freighter, configure or inspect a treasury account, prepare a payment, simulate it, approve it, submit it, and view the result.
Budget: $21,000
Deliverable 3: Testnet Scheduled Payment Relayer
Brief description:
 Build a relayer service for one scheduled treasury payment flow. The relayer submits valid transactions, tracks execution status, logs failures, and cannot custody assets or bypass SmartAccount policy checks. This deliverable is what makes treasury automation more than a generic scheduler: each child execution is bound to a Soroban ledger sequence window and is consumed exactly once, so a relayer retry or duplicate submission cannot replay the same payment a guarantee wallet approvals and signer rules alone do not provide. Transaction building, RPC simulation/submission, and status polling use the official
@stellar/stellar-sdk
 and documented RPC methods rather than a hand-built client; the budgeted work is the scheduling, idempotent job handling, and no-authority operational safeguards specific to STA, not the underlying Stellar client library. The relayer stays self-operated, not a managed third-party service, so visibility into auth handling and replay failure modes is unaffected.
Developer skills and allocation:
 1 backend/relayer developer as primary contributor, with support from 1 Soroban/Rust developer and part-time QA.
Estimated active duration:
 4 weeks
How to measure completion:
 A user can configure a scheduled payment on testnet and verify that the relayer executes it only when SmartAccount policy allows execution.
Budget: $13,500
Tranche 3 (Deliverable Roadmap) - Mainnet
Tranche 3 — Mainnet Launch
Tranche duration: 1.5 months
Development budget allocation: $55,000
Deliverable 1: Mainnet Contracts and TypeScript SDK
Brief description:
 Deploy the smart contracts on Stellar mainnet and release the initial public TypeScript SDK aligned with the deployed contracts. This includes reproducible contract build, mainnet configuration, contract deployment, SDK helpers, transaction preparation helpers, event parsing utilities, and SDK examples. The mainnet-hardening surface is smaller because admin-gating and pause/freeze state rely on an already-audited library rather than custom contract code. The SDK itself starts from the Stellar CLI’s native
contract bindings typescript
 code generation for typed, simulate/sign/submit-ready contract clients, rather than hand-writing that layer; the budgeted work is curating those generated clients and adding event-parsing utilities and examples specific to STA’s policy-version, replay-state, and recovery-state objects, which the SDK exposes as first-class types — not just wrapped wallet-signing calls.
Developer skills and allocation:
 1 Soroban/Rust smart contract developer and 1 backend/relayer developer.
Estimated active duration:
 4 weeks
How to measure completion:
 Mainnet contract addresses are published. A developer can inspect the mainnet configuration and run SDK examples for documented flows.
Budget: $17,000
Deliverable 2: Production dApp and Relayer Monitoring
Brief description:
 Prepare the production dApp and relayer infrastructure for mainnet use. This includes production treasury screens, wallet flows, mainnet transaction simulation, payment execution, relayer deployment, scheduled payment support, transaction status tracking, logs, alerts, monitoring, and fallback procedures. Wallet flows extend the integrated Wallets Kit connection from Tranche 2, which keeps backend support here scoped to RPC/simulation and relayer monitoring rather than auth-protocol work; if passkey signer support is added at this stage, it is integrated from an existing Soroban passkey toolkit rather than built from scratch. Monitoring specifically tracks policy-version changes, replay attempts, and recovery-state transitions — the audit signals unique to STA’s policy/automation/recovery model, beyond generic transaction status.
Developer skills and allocation:
 1 full-stack/frontend developer and 1 backend/relayer developer, with support from 1 Soroban/Rust developer.
Estimated active duration:
 5 weeks
How to measure completion:
 A reviewer can open the production dApp, connect a supported Stellar wallet, operate a mainnet treasury flow, view transaction status, and verify relayer monitoring for the scheduled payment flow.
Budget: $25,400
Deliverable 3: Mainnet Documentation and Testing Support Guide
Brief description:
 Complete the documentation and testing support materials needed for mainnet launch and user testing. This includes operator documentation, SDK usage documentation, deployment notes, testing support guide for core treasury flows, release checklist, issue reporting process, and final handoff documentation. Operator documentation explains the policy-versioning, replay-protection, and recovery model explicitly, so reviewers and operators can see what is enforced on-chain beyond wallet connection and approval steps.
Developer skills and allocation:
 Part-time QA, documentation, deployment, and coordination support, with involvement from all technical contributors for technical accuracy.
Estimated active duration:
 2 weeks
How to measure completion:
 Documentation and testing support materials are available, including the operator guide, SDK usage guide, deployment notes, testing support guide, and release checklist.
Budget: $12,600
Team
Reto Grau – Founder & CEO
Reto Grau brings more than 30 years of experience in asset management, treasury operations, alternative investments, and institutional finance. His career includes senior roles at RMF Investment Management, Man Group, Swiss Life Hedge Fund Partners, JPMorgan, and ABB Treasury. He provides strategic leadership, industry expertise, and access to institutional networks relevant to treasury management, payments, and digital assets.
LinkedIn:
https://www.linkedin.com/in/retograu/
Alexandre Karako – Business Development
Alexandre Karako brings experience in digital assets, fundraising, tokenization, and business development. Through his work with Equisafe, he has contributed to tokenized investment products, issuer onboarding, investor relations, and ecosystem growth. His expertise supports partnerships, adoption, and real-world treasury and tokenization use cases.
LinkedIn:
https://www.linkedin.com/in/alexandrekarako/
Clément Roure – Technical Lead
Clément Roure is an experienced software architect and developer. Through his experience at Keyrock and his work on digital platforms serving more than 400,000 users, he brings expertise in backend systems, scalability, reliability, and financial technology infrastructure.
LinkedIn:
https://www.linkedin.com/in/clementroure/
Maxime Sarthet – Strategic Advisor
Maxime Sarthet is CEO of Equisafe, a leading European tokenization platform. Under his leadership, Equisafe has facilitated €400M+ in investments, supported 25,000+ investors, and enabled 70+ fundraising rounds. He provides strategic guidance and access to adoption channels across the tokenization ecosystem.
LinkedIn:
https://www.linkedin.com/in/maxime-sarthet/
Clement
```
