# ChimpDAO: ChimpDAO: Tap-to-Use Stellar DeFi Card

Source: https://communityfund.stellar.org/submissions/recXeMtBHrhDbdtm7 (SCF #44, awarded $110.0K, category: End-User Application)

- Website: https://chimpdao.xyz/
- Architecture doc: https://docs.google.com/document/d/1ED8c_HOQKdhy9oeB6HDQlywWLLw8nQR4CJVusCPMTfg/edit?usp=sharing

## Links in the submission

- https://docs.google.com/document/d/1ED8c_HOQKdhy9oeB6HDQlywWLLw8nQR4CJVusCPMTfg/edit?usp=sharing
- https://github.com/Consulting-Manao/chimpdao-contracts
- https://github.com/Consulting-Manao/chimpdao-contracts%EF%BF%BCiOS
- https://github.com/Consulting-Manao/chimpdao-ios
- https://drive.google.com/file/d/1La3a3Fji123D81l3T_FTaDdB5gYS5CcB/view?usp=sharing
- https://drive.google.com/file/d/1kHdUxKuAOsTtjThmxcuMOADd0BzbXRMn/view?usp=sharing

## Submission text (as published)

```text
Products & Services
ChimpDAO brings
the first card to Stellar where the hardware itself signs transactions on-chain
. We're building a
hardware-secured Stellar card
 that turns existing Stellar integrations into a physical, tap-to-use product.
The foundation already exists and runs on Stellar mainnet. ChimpDAO's NFC chips generate an elliptic-curve key pair directly in tamper-resistant silicon; the private key never leaves the chip, and an on-chip ECDSA signature authorizes every state-changing call on-chain, with no centralized backend. This grant extends that proven authorization primitive into a card-linked Stellar wallet integrated with the ecosystem's core building blocks.
Concretely, a user can fund a card-linked Stellar account, convert assets through Soroswap, deposit into a DeFindex yield strategy, view their position, and withdraw, each sensitive action authorized by tapping the physical card. Stellar Wallets Kit / Freighter-compatible flows let users fund and move assets between existing Stellar wallets and their ChimpDAO card, so the product is never a closed silo. 
The same primitive unlocks a distinct issuance model: a partner project can fund a card-linked contract account or pending-claim vault, and the recipient claims control through hardware-backed card authorization. This lets Stellar projects distribute branded physical cards already connected to a Stellar-native value mechanism: a yield strategy, a converted asset, or a yield-bearing stable asset, that the holder owns the moment they tap. ChimpDAO never custodies funds and does not guarantee yield; the chip is the sole authorization device and the keys exist only on the card.
The impact for Stellar
: most users still meet DeFi through abstract browser wallets, seed phrases, and protocol-specific UIs. ChimpDAO packages these building blocks into a tap-to-authorize physical object, turning each integration into a real-world mode of using Stellar.
The ChimpDAO Card is our reference product; the same primitive becomes a reusable B2B layer letting partners issue their own branded cards.
Requested Budget
$110.0K
Traction Evidence
ChimpDAO already has a hardware-anchored authorization primitive running on Stellar mainnet.
Our existing Chi//mp system links physical NFC-enabled items to on-chain ownership through secure chip-backed signatures. Each chip generates and stores its own private key directly on the hardware. The private key never leaves the chip. To claim or transfer the associated NFT, the user must physically tap the item: the chip signs an authorization message, and the Soroban contract verifies the signature before allowing the state-changing action.
This primitive is already implemented, open-source, and deployed on Stellar mainnet.
Evidence:
Website:
https://chimpdao.xyz/
Contracts repository:
https://github.com/Consulting-Manao/chimpdao-contracts
IOS repository:
https://github.com/Consulting-Manao/chimpdao-ios
Mainnet collection contract:
CCWQBP7UOTSHMNEVE2P2DCLNI37CFA4WNPMUBNES5BH7QEEEUBXL7Y5Z
Mainnet NFC-NFT contract:
CCTPN4LRCNJBLC3VVEYET7MRLQHSAAXTQG4YBG7W3HORHZFHJIIQ7BLO
ChimpDAO also completed a first real-world pilot through a limited drop of 100 NFC-enabled shirts in collaboration with Palta Labs. This validated the full physical-to-on-chain flow: secure chip sourcing, NFC/APDU communication, native iOS claim flow, SEP-53-inspired signatures, Soroban verification, and NFT ownership display in a Stellar wallet. It also validated the technical and product foundation for physical-to-on-chain ownership.
This grant does not fund the original NFC merchandise primitive retroactively. That foundation already exists. The grant extends it into a Stellar card experience connected to ecosystem integrations.
Moreover, the team is deeply involved in the Stellar ecosystem. Both Pamphile and Barth have been deeply active in the ecosystem for a long time. This gives the team a strong understanding of what Stellar projects need, how ecosystem builders think about integrations, and which UX gaps still prevent Stellar applications from feeling simple and tangible to end users.
We also have already received concrete interest from Stellar ecosystem projects that would like to use ChimpDAO-powered cards if developed. The key use case is simple: a protocol, wallet, community, or brand could issue physical cards linked to its own Stellar utility: funded assets, DeFindex strategies, yield-bearing assets, synthetic assets, or protocol-specific positions. Concrete example: Normal Finance expressed interest in crypto gift-card-like cards linked to protocol-specific assets or positions.
We have also added lightweight partner validation for three of the four named integration paths. At the time of resubmission, we have non-binding Letters of Interest / confirmations covering Soroswap, DeFindex, and Etherfuse:
Soroswap and DeFindex - PaltaLabs LOI link:
https://drive.google.com/file/d/1La3a3Fji123D81l3T_FTaDdB5gYS5CcB/view?usp=sharing
Etherfuse - LOI link:
https://drive.google.com/file/d/1kHdUxKuAOsTtjThmxcuMOADd0BzbXRMn/view?usp=sharing
These confirmations do not create any legal, commercial, financial, exclusivity, or production-support commitment. They simply confirm that the relevant teams are aware of the Chi//mp proposal and open to discussing the proposed integration path if the grant is approved.
For Near Intents, we are currently getting in touch with the relevant team in parallel. Given the short revision timeline, we are not presenting Near Intents as a committed partner at this stage.
ChimpDAO would not custody funds, control private keys, or guarantee yield. The issuing project funds a card-linked account or pending claim vault, and the recipient claims control through hardware-backed card authorization. This turns ChimpDAO into a reusable B2B distribution primitive for Stellar projects: physical cards that carry real on-chain utility, without requiring users to first understand wallets, private keys, swaps, deposits, or DeFi interfaces.
In addition, we also received positive informal feedback from Stellar ecosystem contributors and DevRel members, including Elliot and Bri. Bri explored the concept further by building an Android prototype of the ChimpDAO experience.
Lastly, other ecosystems have already validated the broader category of physical crypto cards and tap-to-use hardware wallets as onboarding and gifting tools. Burner popularized the idea of a low-cost hardware wallet designed for gifting crypto: users tap the card on a phone, view the balance, and start using it without deep crypto expertise. Tangem has shown that card-based self-custody can evolve from passive storage into active financial usage, with in-app features for payments and DeFi access. ChimpDAO brings this proven UX pattern to Stellar with a Stellar-native integration thesis. Burner and Tangem have sold millions of cards.
Tranche 1 (Deliverable Roadmap) - MVP
TRANCHE #1 - Card-Authorized Stellar Wallet, Integration-Ready
This tranche extends ChimpDAO's existing hardware-anchored authorization primitive, already live on Stellar mainnet, into the foundation of the Stellar Card wallet. The goal is not to rebuild the NFC authorization system, which already exists, but to adapt it into a card-linked account model and an integration-ready authorization layer. The technical architecture is finalized in the Technical Architecture Document submitted with this application; this tranche is implementation.
Deliverable 1 - Card Authorization Extension on Testnet
Brief description:
 Adapt the existing chip-backed authorization model into a card wallet foundation: card activation, card public-key registration, nonce/replay protection, lock/revocation state, and signed-intent validation binding amount, destination, and target contract.
How to measure completion:
Contracts deployed to testnet
Card public-key registration, activation state, nonce/replay protection, and lock/revocation implemented
Unit tests passing for valid registration, duplicate-registration rejection, invalid-signature rejection, replayed-nonce rejection, expired-payload rejection, and revoked/locked-card rejection
Testnet contract IDs and example transactions documented
Budget:
 $5,000
Deliverable 2 - iOS Card Wallet Extension
Brief description:
 Extend the existing ChimpDAO iOS app into a card wallet MVP: card activation, card profile, card-linked account display, NFC/APDU signing abstraction, Stellar RPC connection, balance display, transaction review and submission, transaction-status polling.
How to measure completion:
Activation flow + NFC/APDU signing abstraction implemented
Card-linked address and balance displayed on testnet
Transaction-status polling implemented
Error handling for failed scan, invalid card, failed RPC call, failed transaction, and user cancellation
Demo recording of activation and balance display provided
Budget:
 $5,000
Deliverable 3 - External Wallet Interoperability & Funding Flow
Brief description:
 Implement the full mobile funding and interoperability flow so users can move assets from existing Stellar wallets into a ChimpDAO card-linked account, including Stellar Wallets Kit / Freighter-compatible flows where mobile constraints allow. This covers wallet compatibility, balance detection, transaction handling, and error states, ensuring ChimpDAO is never a closed silo. 
How to measure completion:
Card-linked Stellar address viewable in the iOS app
User can fund the card-linked account from an external Stellar wallet
Incoming balance detected and displayed
At least one funding transaction publicly verifiable
Screenshots or demo provided
Functional web surface for partner onboarding and integration docs (not a marketing site).
Budget:
 $5,000
Deliverable 4 - Integration Adapter Layer for Stellar Protocols
Brief description:
 Build the adapter abstraction that lets Stellar integrations plug into the card-authorization model, with DeFindex and Soroswap as the primary adapter targets and Etherfuse and Near Intents as secondary integration paths. This includes shared transaction-preparation logic, signed-intent schemas, and payload structures that bind asset, amount, target contract, route, nonce, and expiration into the card-authorized message.
How to measure completion:
Adapter interfaces implemented and documented
DeFindex and Soroswap stubs wired to the signing layer
Test vectors passing for modified amount, modified asset, modified destination, wrong target contract, replayed nonce, and expired payload
Integration-developer notes published in the repository
Budget:
 $4,000
Deliverable 5 - Physical Card Form-Factor Technical Validation
Brief description:
 Validate the transition from NFC-tagged merchandise to a physical card form factor with our existing secure-chip partner. This includes technical coordination on chip placement, antenna behavior, NFC reliability, card material constraints, card thickness, and the minimum physical requirements needed for secure APDU communication and on-chip signing. The goal is to make sure the hardware-secured authorization primitive can work reliably in a card format, not only inside merchandise.
How to measure completion:
Card form-factor technical specification documented, including chip, antenna, NFC constraints, and card material requirements
Technical coordination completed with the existing secure-chip partner
At least one physical card test sample or equivalent form-factor prototype produced
NFC scan and on-chip signing validated on the card sample or prototype
Results documented, including limitations, manufacturing constraints, and next steps for production readiness
Budget:
 $3,000
Deliverable 6 - Integration Scope Finalization & Partner Coordination
Brief description
: Finalize the technical scope for the funded integration paths before implementation starts. This includes Soroswap, DeFindex, Etherfuse, and Near Intents, with a clear distinction between partner-validated integrations and lightweight technical proof-of-concept paths. The goal is to confirm the exact supported flows, technical assumptions, available environments, constraints, and communication channels needed to execute the grant properly.
How to measure completion:
Soroswap and DeFindex integration scope confirmed with PaltaLabs
Etherfuse core-light asset holding / display scope confirmed based on available supported assets and environments
Near Intents cross-chain funding scope reviewed using available public API / documentation and ongoing contact with the relevant team
Integration assumptions, dependencies, risks, and limitations documented
Updated integration scope reflected in the technical architecture document
Budget
: $5,000
Deliverable 7 - Card Product Implementation Plan & Signed-Intent Specification
Brief description:
 Prepare the implementation plan required to move from the existing NFC merchandise primitive to the funded Stellar Card product. This includes finalizing the card-linked account model, signed-intent payload structure, UX flows, testnet deployment plan, and physical card validation plan before the deeper implementation work continues.
How to measure completion:
Card-linked account model documented
Signed-intent payload structure finalized for funding, swaps, deposits, withdrawals, and asset display flows
iOS implementation tasks defined for activation, balance display, NFC signing, transaction review, and integration screens
Testnet deployment plan prepared
Physical card validation plan prepared, including card form-factor requirements and secure-chip constraints
Budget
: $6,000
Tranche 2 (Deliverable Roadmap) - Testnet
TRANCHE #2 - Testnet Integrations: Tap-to-Swap, Tap-to-Yield, Tap-to-Fund
This tranche turns the card from a wallet foundation into a working Stellar integration surface: fund, swap, deposit into yield, track, withdraw: every sensitive action authorized by tapping the physical card. Non-custodial throughout: keys exist only on the chip; ChimpDAO never custodies funds and does not guarantee yield.
Deliverable 1 - DeFindex Integration: Strategy Display, Deposit, Position Tracking & Withdrawal
Brief description:
 Integrate DeFindex as the primary yield layer. Users view supported strategies, authorize a deposit with the card, view the resulting position, and withdraw via the same card-backed model. Blend exposure is obtained for free through DeFindex strategies that include it - no separate Blend integration is promised.
How to measure completion:
Supported strategies displayed
Deposit and withdrawal preparation implemented
Card-backed authorization payloads for both
At least one deposit and one withdrawal transaction publicly verifiable on testnet
Position display implemented
Non-guaranteed-yield disclaimer shown
Budget:
 $13,000
Deliverable 2 - Soroswap Integration: Route-Bound Asset Conversion
Brief description:
 Integrate Soroswap so a card funded in one asset can convert into the asset a strategy requires: quote/route retrieval, swap review, slippage display, route-bound payload signing, and card-backed swap authorization.
How to measure completion:
Quote/route retrieval implemented
Swap-review screen displays asset in, asset out, expected output, slippage, and route
Route hash/context included in the signed payload
Card-backed swap authorization implemented
At least one verified testnet swap
Balance updated post-swap
Error handling for insufficient balance, route failure, slippage failure, and transaction failure
Budget:
 $8,000
Deliverable 3 - Etherfuse Core-Light Support: Yield-Bearing Stable Asset Holding & Accrual Display
Brief description:
 Support holding an Etherfuse stablebond or supported Etherfuse asset in a card-linked account and displaying balance and accrual information in the available testnet, devnet, or supported integration environment. This demonstrates a lightweight "card with yield-bearing asset support" use case without ChimpDAO operating an investment product.
How to measure completion:
Card-linked account can hold a supported Etherfuse asset in the available integration environment
Balance and accrual-related information displayed in the app
Non-custodial, non-advice, and non-guaranteed-yield disclaimers shown
Limitations around asset availability, jurisdiction, or mainnet support documented
Budget:
 $4,000
Deliverable 4 - Near Intents Cross-Chain Funding Proof of Concept
Brief description:
 Build a focused proof of concept showing how a user can fund a ChimpDAO card-linked Stellar account from another ecosystem through the Near Intents 1Click API. The goal is to demonstrate that a Stellar card can be topped up from outside Stellar without turning ChimpDAO into a fiat ramp or custodial service.
How to measure completion:
At least one real cross-chain funding flow to a card-linked Stellar account is executed and documented in the available Near Intents environment (testnet or mainnet), with the resulting Stellar-side balance detected and displayed in the app
If the Near Intents environment is unavailable during the tranche, the deliverable will include a documented, runnable integration against the Near Intents API, with the limitation clearly reported
Budget:
 $3,000
Deliverable 5 - End-to-End Card-Authorized Integration Flow & Reviewer Demo Package
Brief description:
 Connect the iOS app, card signing, authorization contracts, and protocol integrations into a complete end-to-end testnet flow: activate card -> fund card-linked account -> swap via Soroswap -> deposit into a DeFindex strategy -> view position -> withdraw, each action card-authorized. The partner-issued card / pending-claim vault flow (building on the existing prize reference contract) is also demonstrated. Ship a reviewer demo package.
How to measure completion:
End-to-end testnet demo recorded
Each sensitive action requires card-backed authorization
Partner-issued card / pending-claim vault flow demonstrated
Testnet demo guide published with contract addresses, transaction links, screenshots, and known limitations
Reviewer checklist covering activation, funding, Soroswap swap, DeFindex deposit/withdrawal, Etherfuse display, and Near Intents PoC
Budget:
 $5,000
Tranche 3 (Deliverable Roadmap) - Mainnet
TRANCHE #3 - Mainnet Launch of the ChimpDAO Stellar Card
This final tranche brings the card from testnet validation to a mainnet-ready product: launch the authorization system, ship a reviewer-accessible iOS build, complete the core Stellar integrations on mainnet, run professional user testing, and publish a full verification package. Security-audit credits are provided at Tranche #3 completion per SCF: no audit cost is included in this budget.
Deliverable 1 - Mainnet Card Authorization System
Brief description:
 Finalize, harden, and deploy the card-authorization contract system to mainnet: card registry, activation logic, card-linked authorization layer, nonce/replay protection, lock/revocation state, policy checks, and integration authorization components.
How to measure completion:
Final contracts deployed to mainnet
Mainnet contract IDs publicly documented
Deployment scripts and configuration files published
Contract README updated with architecture, function descriptions, and usage
At least one mainnet card-activation/registration transaction and one mainnet card-backed authorization transaction documented
Test suite passing for activation, signature verification, replay rejection, expired-payload rejection, revoked/locked-card rejection, policy enforcement, and integration authorization
Operational runbook published (deployment, key parameters, monitoring)
Incident response plan documented (contract pause/lock path, card revocation procedure, partner communication)
Budget:
 $9,000
Deliverable 2 - iOS Mainnet Release Candidate
Brief description:
 Deliver a mainnet-ready iOS release candidate: card activation, dashboard, card-linked account and balance display, transaction review, NFC signing in production configuration, transaction submission, transaction history, and integration screens.
How to measure completion:
TestFlight (or equivalent reviewer-accessible) build delivered
Mainnet activation and balance display working
NFC signing works in production
Transaction-review screens for transfers, swaps, deposits, and withdrawals
Transaction status and history displayed
Error handling for failed scan, rejected signature, failed simulation, failed submission, insufficient balance, failed swap/deposit/withdraw
Mainnet demo video provided
Budget:
 $9,000
Deliverable 3 - Mainnet DeFi Integrations: DeFindex + Soroswap, with Etherfuse Core-Light Support Where Available
Brief description:
 Launch the funded integrations in mainnet configuration. DeFindex deposit/withdrawal and Soroswap conversion are the required mainnet integrations, each card-authorized; Etherfuse remains core-light asset holding/accrual display where available. Route context is bound to the signed payload for swaps.
How to measure completion:
Supported DeFindex strategy displayed
At least one mainnet DeFindex deposit and one withdrawal documented
At least one mainnet Soroswap swap documented
Card-backed authorization required for swap, deposit, and withdrawal
Etherfuse holding/display demonstrated where available
Position display updated
Non-guaranteed-yield and non-advice disclaimers present
Budget:
 $13,000
Deliverable 4 - Security Hardening & Failure-Mode Testing
Brief description:
 Complete internal security hardening and failure-mode testing (not a paid security audit): prove the card wallet rejects invalid or dangerous actions and handles failure cases safely.
How to measure completion:
Tests completed and documented for replay attack, invalid signature, modified amount, modified asset, modified destination, wrong target contract, expired payload, revoked card, locked card, and failed swap/deposit/withdraw handling
Internal security checklist published or summarized
No paid audit costs included
Budget:
 $6,000
Deliverable 5 - Partner Testing, Product Surface and Final Verification Package
Brief description:
 Run professional product testing on the main card flow and prepare the final reviewer / partner verification package. Testing will include at least one ecosystem partner-led session focused on a branded card use case, such as a partner issuing a card linked to its own asset, DeFi strategy, or protocol-specific position. This is product validation only: no marketing, promotion, token incentives, or user acquisition.
This deliverable also includes a reviewer-accessible product surface that explains the card experience, partner card use cases, supported integrations, repositories, mainnet contracts, demo links, and known limitations. This is not a marketing campaign; it is a product and documentation surface for reviewers, partners, and technical validation.
How to measure completion:
At least one partner-led test session completed with a Stellar ecosystem project or partner use case
Feedback documented on onboarding clarity, card activation, funding, transaction review, NFC signing, protocol integration, and non-custodial understanding
Priority UX fixes implemented based on testing feedback
Reviewer-accessible product surface published with product overview, supported integrations, demo links, repositories, mainnet contract addresses, and known limitations
Final technical architecture document updated
Mainnet transaction examples published
iOS demo video published
User guide and integration guide published
Reviewer verification checklist published with links to contracts, transactions, repositories, app demo, and partner testing summary
Future interoperability roadmap documented, including potential exploration with existing Stellar card or wallet products such as Decaf, without committing this grant to a full Decaf integration
Budget:
 $7,000
Team
ChimpDAO is led by Barth Houot and Pamphile Roy, combining product, branding, Web3, Stellar ecosystem experience, and deep technical expertise.
Pamphile Roy is a Principal Software Engineer at The Aha Company and provides consulting services through Consulting Manao GmbH. He previously worked as a senior Engineer at Bitpanda, the largest crypto broker in EU; deployed transformer models to more than 10 million users at iTranslate and contributed to AI/ML solutions for Microsoft Flight Simulator 2020. Within the Scientific Python ecosystem, Pamphile is widely known for his open-source contributions, serving as a maintainer of SciPy, which receives millions of daily downloads, and SALib, while also participating in the Scientific Python Steering Committee. Pamphile has been active in the Stellar community for the last two years, contributed to Stellar developer documentation, participated in two SEPs, and received multiple SCF grants for Tansu, his Soroban-based project. He is a long-time Stellar ecosystem contributor and an SCF Pilot and an SCF Delegate / project reviewer.
Barth Houot is a French engineer, developer, and Web3 entrepreneur with a background in product development, branding, e-commerce, and digital marketing. He has led several projects combining user experience, technical innovation, and go-to-market execution. His experience with Stellar, product design, community-driven brands, and physical merchandise positions him to lead ChimpDAO’s product direction, user experience, and go-to-market strategy. Barth is also an SCF Pilot and an SCF Delegate / project reviewer.
Together, the team has the right combination of hardware-secured blockchain experience, Stellar ecosystem knowledge, mobile app development, smart contract architecture, product strategy, and community credibility to execute this integration.
Socials:
Tupui: 
Linkedin: 
https://www.linkedin.com/in/tupui/
Github:
https://github.com/tupui
Barth: 
Linkedin:
https://www.linkedin.com/in/barthhouot/
Github:
https://github.com/Grominet95
Barth Houot
Tupui
```
