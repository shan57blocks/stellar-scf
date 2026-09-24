# Account Demolisher: Account Demolisher

Source: https://communityfund.stellar.org/submissions/recqvIs2iRu34ESGo (SCF #44, awarded $120.0K, category: End-User Application)

- Website: https://demolisher.saliht.xyz
- Architecture doc: https://drive.google.com/file/d/1qRNRr3kYomaQJ-0z9uIl2DmaiQE2OI1r/view?usp=sharing

## Links in the submission

- https://demolisher.saliht.xyz
- https://drive.google.com/file/d/1qRNRr3kYomaQJ-0z9uIl2DmaiQE2OI1r/view?usp=sharing
- https://github.com/bytemaster333/account-demolisher
- https://demolisher.saliht.xyz/
- https://github.com/bytemaster333/Soroban-ELK
- https://github.com/bytemaster333/Hashirama
- https://github.com/bytemaster333/StylusVerify
- https://github.com/bytemaster333/SentinelBag

## Submission text (as published)

```text
Products & Services
Stellar carries more than 10 million accounts, and a large share of them are stale or abandoned, the so-called zombie accounts that still lock reserves no one is using. Opening an account is trivial; closing one to recover its funds is not. Every account holds a 1 XLM minimum balance, plus 0.5 XLM locked for each subentry it holds: every trustline, offer, extra signer, data entry, and claimable-balance claim. Reclaiming that value means removing every subentry and then running
ACCOUNT_MERGE
.
This is hard to do by hand.
ACCOUNT_MERGE
 requires the account's highest signing authority and fails while even a single trustline or offer remains, so the full teardown is a multi-step sequence most users cannot assemble safely without risking loss of funds. It compounds when funds are headed to a centralized exchange: no major exchange accepts an incoming
ACCOUNT_MERGE
, so the final 1 XLM base reserve is effectively frozen for anyone consolidating there.
A second gap has widened as value has moved into Soroban: lending positions on Blend, pooled liquidity on Aquarius and Soroswap, vault collateral in FxDAO, and balances held inside custom token contracts. The one public precedent for account closure, the MIT-licensed
stellar.expert/demolisher
 by StellarExpert, predates Soroban and handles only the classic side of Stellar, so it cannot see or unwind any of these positions. It also leaves classic gaps unaddressed, with no sponsorship checks, no claimable-balance handling, no classic liquidity-pool withdrawal, and raw secret-key entry as its only signing path.
This submission funds Account Demolisher, the Soroban-era successor to that tool: a production-grade web tool that takes an account from any starting state to cleanly closed in one guided session, across both the classic surface and the Soroban DeFi surface the prior tool cannot reach. The classic path preserves and modernizes the proven flow of the existing open-source tool, reused selectively and with attribution, and extends it to everything that tool leaves out. Because account closure is the most destructive action a user can take on Stellar, the design minimizes the trust it asks for: every transaction is signed on the user's own device, the secret key never reaches a server, the full plan is previewed before anything is signed, the code is open-source and externally audited, and the tool refuses to attempt anything it cannot finish safely.
A working, open-source prototype already runs this entire flow end-to-end on Stellar's testnet, with a live demo at
https://demolisher.saliht.xyz/
 and full source at
https://github.com/bytemaster333/account-demolisher
, so the work below productionizes the running system rather than starting from a blank slate. The project comprises:
Complete closure across both execution models:
 Cancel DEX offers, withdraw classic and Soroban liquidity and lending positions, claim selected claimable balances, remove trustlines and data entries, reduce signers and reset thresholds, convert balances to XLM or a chosen base asset, and merge the account, either to a wallet directly or to an exchange through a mediator account. The closure is a sequence of standard, auditable Stellar transactions rather than a custodial service, collapsing an error-prone manual process into one preview-first flow.
Soroban DeFi position unwinding:
 Exit positions across Blend, Aquarius, Soroswap, and FxDAO, each through its own dependency-correct path, plus generic draining of smart-contract token balances. This is the central differentiator and the part the prior art does not address at all.
Mediator-based merge for exchange destinations:
 Because exchanges reject
ACCOUNT_MERGE
, a closure targeting an exchange routes through a temporary mediator account that forwards funds as a memo-carrying payment, recovering the final 1 XLM base reserve that is otherwise unreachable. The strict transaction-shape validation that keeps this step safe is inherited from the existing tool and hardened further.
Token allowance visibility and revocation:
 A standalone, read-only viewer lists every standing SEP-41 allowance an address has granted and lets the user revoke any of them without closing the account. It is the Stellar analogue of
revoke.cash
 and a genuine protection against a known Soroban exploit vector: stale or malicious approvals.
Safe, preview-first execution:
 An inspectable, simulated plan with explicit warnings and typed destination confirmation, and honest handling of cases a single session cannot finish, such as a multi-day Blend withdrawal queue, and of accounts that can never merge at all, such as those with the
AUTH_IMMUTABLE
 flag set.
An open, reusable foundation:
 Shipped under Apache 2.0, with the teardown logic, the wallet integration, the DeFi-position-data integration, and the mediator pattern structured for reuse by wallets, exchanges, and other builders.
Requested Budget
$120.0K
Traction Evidence
Demand is documented rather than assumed, and recorded inside Stellar's own repositories. Two of them request a demolisher-style helper outright:
js-stellar-wallets
 in issue #98, and the Go SDK in issue #1587, titled "Account 'demolisher' helper". The
stellar-protocol
 repository carries long-standing requests to make merging easier in issues #56 and #324. The workflow is already used in practice: the classic-only
stellar.expert/demolisher
 is the tool community members point to for recovering leftover XLM, for instance when users needed to pull their balances out of Keybase after it dropped Stellar support. LOBSTR's Merge Tool and Scopuly's per-trustline cleanup serve the same classic-only need at scale, while none of them touch the Soroban surface, allowance revocation, or a non-custodial multisig flow. The same pattern is established on other chains: Solana, whose accounts also lock a reclaimable reserve, sustains an active category of reclamation tools such as Sol Incinerator.
A working prototype already exists and is open-source. It runs the full closure flow end-to-end on Stellar's testnet: classic teardown and merge, Soroban token draining and allowance revocation, DeFi position unwinding across Blend, Aquarius, Soroswap, and FxDAO, multisig coordination through Refractor and partial-XDR, the mediator-based exchange merge, and the dry-run plan tree, all with client-side signing. A live instance runs at
https://demolisher.saliht.xyz/
, and the full source is public and open-source on GitHub at
https://github.com/bytemaster333/account-demolisher
. This is what de-risks the proposal: it shows the architecture works and that the whole surface can be shipped, not just described. The grant funds taking the prototype to production. The production tool is the deliverable; the prototype is the evidence it can be built.
The work is a consumer of existing SCF investment, not a duplicate of it. For position discovery it draws on one of the SCF #41-funded DeFi Position APIs, either Orion or OctoPos, both Build awards from Q1 2026 that normalize a Stellar account's DeFi holdings across Blend, Aquarius, Soroswap, and FxDAO into a single read. Because neither API is guaranteed to be in production when this is reviewed, the tool reaches whichever it uses through a provider abstraction backed by a direct on-chain-read fallback, so it stays fully functional either way and serves as a clean reference consumer for whichever matures first.
Tranche 1 (Deliverable Roadmap) - MVP
Full classic account closure on Stellar's testnet, plus the discovery and execution foundation the Soroban work builds on. Estimated completion: end of Month 1.5, around Week 6. Tranche total: $24,000.
Discovery and feasibility-audit layer:
 A read-only inventory through Horizon and Soroban RPC, covering balances, trustlines including pool-share trustlines, offers, data entries, signers and thresholds, sponsorship counts, flags, and claimable balances. From it the tool produces a feasibility verdict, hard-stops on the
AUTH_IMMUTABLE
flag and on any account that sponsors others, with a
numSponsoring
 count above zero, and analyzes the signer set with
tx-signers-inspector
.
Completion:
 against a corpus of testnet accounts spanning typical and adversarial configurations, the audit returns a correct inventory and verdict for each, including correct refusal of
AUTH_IMMUTABLE
 and sponsoring accounts.
Budget:
 $8,000.
Classic demolition engine and mediator merge:
 A dependency-ordered teardown that cancels offers, withdraws classic liquidity-pool positions, claims selected claimable balances, converts to XLM via
PATH_PAYMENT_STRICT_SEND
, removes trustlines with pool-share trustlines first, deletes data entries, revokes held sponsorships, reduces signers and resets thresholds via
SET_OPTIONS
, and runs
ACCOUNT_MERGE
, splitting automatically past the 100-operation limit. For exchange destinations, it adds the mediator-account pattern with its strict two-operation envelope validator.
Completion:
 testnet accounts carrying trustlines, offers, and data entries close to a deleted state, with Horizon returning a 404 for the account after the merge; an exchange-destination closure routes the full balance and memo through the mediator, and the validator rejects malformed and fee-bumped envelopes.
Budget:
 $10,000.
Wallet integration and closure UI:
 Connectivity through
stellar-wallets-kit
, covering Freighter and Ledger, xBull, Albedo, Rabet, LOBSTR, Hana, and WalletConnect, with the warned in-memory secret-key fallback; and a five-step interface: connect, configure destination and memo, preview, confirm, execute.
Completion:
 a user completes a full classic demolition on the testnet through the interface, and an equivalent closure through the secret-key fallback passes in automated browser tests.
Budget:
 $6,000.
Tranche 2 (Deliverable Roadmap) - Testnet
The Soroban differentiator: full token and DeFi coverage, the position-discovery and routing layer, the dry-run preview and multisig coordination, and the safety suite. Estimated completion: end of Month 3, around Week 12. Tranche total: $36,000.
Soroban token surface: discovery, drain, and allowances.
 SEP-41 balance discovery by simulation, Stellar Asset Contract versus custom-token handling, per-token
transfer
 draining, allowance enumeration by event scan, per-allowance revocation, and the standalone allowance viewer.
Completion:
 a testnet account holding custom SEP-41 tokens and active allowances is drained and its selected allowances zeroed, verified on-chain, and the standalone viewer lists and revokes allowances for any address independently of any closure.
Budget:
 $8,000.
DeFi position unwinding across Blend, Aquarius, Soroswap, and FxDAO:
 Per-protocol exit logic: Blend repay, collateral and supply withdrawal, emissions claim, and the honestly queued 17-day backstop withdrawal with its unlock date; Aquarius direct-router withdrawal, claim, and budget-bounded swaps; Soroswap liquidity removal and aggregator conversion to XLM; and FxDAO's hybrid Soroban repay-and-redeem followed by classic stablecoin disposal. Other protocols are surfaced with a "manual close required" notice.
Completion:
 testnet accounts carrying Blend positions and, where testnet liquidity allows, Aquarius, Soroswap, and FxDAO positions are unwound with proceeds routed to XLM, and the backstop position is correctly queued with its unlock date shown.
Budget:
 $12,000.
Position-discovery abstraction and routing layer:
 A single provider interface with three schema-validated sources: Orion as primary, OctoPos as secondary, and a direct-contract reader as fallback. The hybrid routing layer uses native path payment for classic assets and the Soroswap aggregator for Soroban tokens, with configurable slippage and pre-signature re-simulation.
Completion:
 discovery returns consistent results between the direct-contract provider and an API provider when available; conversions respect the slippage bound and refuse value-destroying sells.
Budget:
 $7,000.
Dry-run plan tree, orchestration, multisig coordination, and safety suite:
 The plan tree, comprising dependency-ordered generation, per-Soroban-step simulation, in-memory classic validation, and live re-preview, with its timeline UI; the XState state machine with typed failure recovery; multisig coordination via Refractor, partial-XDR import and export with hash-bound signature merging, and sequential signing; and the safety controls: typed confirmation with timed delay, high-value warning, memo enforcement, scam-token heuristics, a fee cap, and sign-time allow-list enforcement.
Completion:
 a mixed-position testnet closure renders a correct plan tree, simulates each step, recovers from injected failures such as a stale sequence number or a changed footprint, completes a 2-of-3 multisig closure through both Refractor and partial-XDR, and triggers each safety gate in automated tests.
Budget:
 $9,000.
Tranche 3 (Deliverable Roadmap) - Mainnet
Production hardening, the security audit and its remediation, the full test suite, documentation, and the mainnet launch. Estimated completion: end of Month 4, around Week 16. Tranche total: $48,000.
Production hardening and mainnet deployment:
 Strict content-security policy, subresource integrity, version-pinned and provenance-attested releases, and server-route rate limiting; the finalized mainnet contract-address allow-list for Blend, Aquarius, Soroswap, and FxDAO, with each entry citing its upstream source; and deployment under a canonical domain with a SEP-1
stellar.toml
, served from a CDN with an optional IPFS mirror.
Completion:
 the tool is deployed to mainnet at the canonical URL and closes real accounts carrying both classic subentries and Soroban DeFi positions; allow-list enforcement is covered by an automated source-level test; security headers are verified on the mainnet deployment.
Budget:
 $14,000.
Full test suite, adversarial and property-based:
 Unit tests covering plan generation, position-to-operation mapping, routing and slippage math, and transaction construction; integration tests against the live testnet; browser end-to-end tests with an accessibility sweep; property-based fuzzing that runs the planner against many randomized account states to assert that no such state yields a sequence that could strand an account, plus cycle detection; and an adversarial corpus covering
AUTH_IMMUTABLE
 and sponsoring refusals, high-subentry transaction splitting, mid-flow state changes, deleted asset issuers, and non-compliant token contracts.
Completion:
 the full suite passes in CI, including the fuzzing and adversarial corpus runs.
Budget:
 $16,000.
Security audit remediation:
 Engineering time to remediate findings from the Audit Bank audit, gated as a release blocker; audit scope covers mediator-key handling and the merge-envelope validator, plan-generation correctness, transaction and signature handling, and an SDK supply-chain review. The audit itself is provided through Audit Bank and is not part of this budget.
Completion:
 all high and critical findings are resolved or formally accepted, re-verified in the codebase, with a remediation summary published.
Budget:
 $10,000.
Documentation:
 A user guide, an integrator guide for wallets and exchanges, an architecture reference, and a security reference including the threat model.
Completion:
 the documentation set is published in the repository and covers each user flow and integration path.
Budget:
 $8,000.
Team
Salih Toruner
Salih Toruner is an infrastructure and developer-tooling engineer building across multiple blockchain ecosystems. A Linux system administrator with a degree in Electrical & Electronics Engineering, he ships full-stack systems end to end — from Kubernetes appchain operators and node-operations automation to build-verification and on-chain security tooling. He is a two-time ecosystem grantee (Stellar Community Fund #29 and Arbitrum), working solo to take projects from architecture through public release in Rust, Go, Python, and TypeScript.
Projects
Stellar Command Insights
: Full-stack developer-analytics platform for Stellar/Soroban - CLI/RPC log pipeline on the ELK stack with a real-time dashboard, Telegram/Slack alerting, and one-line installers. Funded by the Stellar Community Fund (SCF #29)
Hashirama
: Production-grade Kubernetes operator that deploys and manages Starknet Madara L3 appchains from a single YAML, with a built-in UI dashboard
StylusVerify
: Deterministic source-verification tooling for Arbitrum Stylus (Rust/WASM) contracts — proves on-chain WASM bytecode matches its GitHub source for trustless build integrity
SentinelBag
: On-chain integrity engine for Solana tokens — detects wash-trading, sybil attacks, and inorganic volume using "Proof of Ecosystem" heuristics and unique-sender analysis
Salih Toruner
```
