# LumenWipe: LumenWipe - Account Demolisher

Source: https://communityfund.stellar.org/submissions/recuKWaSdUL8Lkw9o (SCF #44, awarded $120.0K, category: Developer Tooling)

- Website: https://www.lumenwipe.com
- Architecture doc: https://docs.lumenwipe.com/architecture

## Links in the submission

- https://docs.lumenwipe.com/architecture
- https://github.com/LumenWipe/lumenwipe
- https://stellar.expert/demolisher/public
- https://drive.google.com/file/d/1VrRXZOwgN51TRduPM2lzI9Hbz5ooDovF/view?usp=share_link
- https://stellar.expert/explorer/public/tx/270677660857139200
- https://stellar.expert/explorer/testnet/account/GALYU2WHUH5HV3BAYTOP5ZMUJWXF43QLSVFTEMBYCWF6MM5JDFHXFESQ
- https://docs.lumenwipe.com
- https://github.com/akkuea/akkuea
- https://github.com/salazarsebas/Cougr
- https://github.com/kommitters/stellar_sdk
- https://github.com/kommitters/chaincerts-smart-contracts
- https://github.com/kommitters/soroban-did-contract
- https://github.com/salazarsebas/stellar-explorer

## Submission text (as published)

```text
Products & Services
This submission responds to the Account Demolisher RFP. LumenWipe is built by a two-time SCF Build Award team with a classic account wind-down already live on mainnet; this award funds Soroban and DeFi parity, the REST API and SDK, and production hardening.
We studied
stellar.expert/demolisher/public
 (Orbit Lens) in depth before building - its step-ordering logic informed our architecture. LumenWipe extends it with full Soroban support, five DeFi protocol exits, sponsored fees for reserve-locked accounts, and an API/SDK layer the existing tool lacks.
What this submission delivers:
- Claimable balances and per-asset dispositions. User-selected claiming with predicate and sponsorship handling; per-asset choice of convert, transfer, or burn before ChangeTrust removal.
- DeFi position exits: Blend, Aquarius, Soroswap, Phoenix, and FxDAO - two protocols beyond the RFP minimum. Positions sourced from OctoPos or Orion for mainnet reads; direct getLedgerEntries as testnet and fallback path. Every exit built client-side as an InvokeHostFunction and simulated via simulateTransaction before signing.
- Soroban token conversion. SEP-41 balances swap to XLM or a user-specified base asset through the Soroswap API; API-built XDR verified client-side before signing.
- Allowance inspector. SEP-41 allowance discovery via getEvents and a known-contract registry; one-click revocation without closing the account.
- Sponsored fees. CAP-15 fee-bump envelopes for accounts locked at minimum reserve - the accounts that need this tool most.
- Exchange-safe merges. CEX merges run through a shared mediator in one atomic transaction - a deliberate improvement over the RFP's temporary mediator: reserve paid once by the operator, so the user recovers essentially all XLM. Memo type enforced from an open-source exchange registry.
- REST API and TypeScript SDK. Programmatic wind-down for wallets and platforms; first integration partner Pollar has a signed LOI.
Signing layer: stellar-wallets-kit (SEP-43), in-memory secret key mode, and multi-key multisig gathering. Secret keys never reach a server.
Dry-run approach: a full deterministic plan is computed from live account state before any transaction is built - the user sees every step before signing anything. Each transaction is then simulated via simulateTransaction immediately before the signing prompt. For most accounts the multi-step nature makes a single atomic dry-run impractical; this two-layer model provides equivalent safety guarantees.
Maintenance: Apache 2.0 open source; integration partnerships (Pollar LOI is the first) sustain ongoing development; volume API/SDK usage carries a small fee, keeping the tool free for individual users; the static frontend and serverless read-only API keep operating costs minimal; the wasmHash contract registry means adding a new protocol version is a registry update, not a code change.
Decentralization: all transactions built and signed in the browser; the server has no path to user keys or funds. The only centralized components are two narrow backend keys (mediator co-sign, fee-bump sponsor) whose blast radius is structurally bounded - neither can initiate a transaction or redirect funds. The exchange registry is open-source and self-hostable.
Infrastructure: static Next.js frontend on Vercel CDN; serverless read-only API routes using Stellar RPC (simulation, submission, live reads) and Horizon (offers, full account state, classic path-finding), plus the mediator co-sign endpoint; Vercel KV for a single merge-count integer. No databases, no user data at rest.
Privacy: fully non-custodial - no wallet addresses, account states, or transaction details stored server-side. The fee-bump endpoint applies per-IP rate limiting for abuse prevention; IPs are not logged or retained.
Community updates: monthly updates in the
LumenWipe Matrix room
,
Discord
, and the LumenWipe blog, covering tranche progress, blockers, and on-chain evidence.
Licensing: Apache 2.0. Full repository:
https://github.com/LumenWipe/lumenwipe
.
Requested Budget
$120.0K
Traction Evidence
Signed LOI with
Pollar
 - an SDK for onboarding users and enabling payments on Stellar - to integrate LumenWipe into its account closure path, recovering XLM back to the master account that funded each user wallet. Publicly available, business terms redacted:
https://drive.google.com/file/d/1VrRXZOwgN51TRduPM2lzI9Hbz5ooDovF/view?usp=share_link
Launched less than two weeks ago. Since launch: 10+ accounts closed on mainnet with 100+ XLM in base reserves recovered; 50+ accounts closed on testnet; 300+ unique visitors. Example end-to-end mainnet closure:
https://stellar.expert/explorer/public/tx/270677660857139200
. Public analytics:
Link on the umami platform
.
Open-source codebase, Apache 2.0:
https://github.com/LumenWipe/lumenwipe
 - deterministic plan builder, step executor with per-step confirmation, IndexedDB session recovery reconciled against on-chain state, exchange registry with memo enforcement, the mediator flow, unit tests, and Playwright end-to-end tests against testnet:
https://stellar.expert/explorer/testnet/account/GALYU2WHUH5HV3BAYTOP5ZMUJWXF43QLSVFTEMBYCWF6MM5JDFHXFESQ
Documentation:
https://docs.lumenwipe.com
Community:
Matrix
 ·
Discord
Tranche 1 (Deliverable Roadmap) - MVP
Tranche 1 - Classic completion (MVP). Total: $24,000.
T1 closes the classic account closure path end to end - the only path executable without DeFi protocol risk in the MVP phase, so it can be independently verified before Soroban and DeFi complexity is layered in T2. The working classic wind-down (live on mainnet, see Traction Evidence) is the starting point, not a deliverable; every item below is future work.
1. Claimable balances, end to end
- Brief description: Enumerate claimable balances (claim predicates and sponsorship implications included), user-selected claiming in the plan, ClaimClaimableBalance execution, and created-balance sponsorships detected as merge blockers.
- How to measure completion: On testnet, an account holding claimable balances closes end to end with selected balances claimed; an account sponsoring a claimable balance is blocked with a plain-language explanation. Reproducible via the public E2E suite.
- Budget: $7,500
2. Per-asset dispositions
- Brief description: Per-asset choice of convert, transfer as-is to a destination holding the trustline, or burn to issuer - applied before ChangeTrust removal for each non-XLM balance in the plan.
- How to measure completion: Testnet close where one asset converts, one transfers, and one burns, each confirmed independently; the E2E suite covers the three-path scenario.
- Budget: $5,500
3. Account state provider
- Brief description: Pluggable Horizon-compatible provider for offer and data-entry enumeration, with numSubEntries reconciliation as the completeness check - a mismatch between indexed count and on-chain count surfaces a plan-build blocker instead of producing a silent incomplete plan.
- How to measure completion: A testnet account whose indexed subentry count diverges from the live numSubEntries value demonstrably surfaces a blocker rather than proceeding; provider is swappable without code changes, verified by an integration test that stubs the provider.
- Budget: $4,500
4. Multisig hardening
- Brief description: Signature gathering across several wallets and keys on one envelope, covering ed25519 signers, with hash(x) and pre-auth signer types surfaced with manual-path guidance.
- How to measure completion: A 2-of-3 multisig account closed on testnet using two different wallets in a recorded demo; the E2E suite covers the multisig path.
- Budget: $6,500
Tranche 2 (Deliverable Roadmap) - Testnet
Tranche 2 - Soroban and DeFi parity (Testnet). Total: $36,000.
1. DeFi position detection via OctoPos or Orion, with testnet fallback
- Brief description: Adapter over OctoPos or Orion (both funded DeFi Position API recipients) normalizing positions across protocols, freshness metadata, and partial-result flags. The primary provider for mainnet reads is selected based on readiness at integration time; direct contract reads via getLedgerEntries serve as the testnet path, keeping degraded-mode coverage under permanent CI.
- How to measure completion: Positions for a seeded account detected on mainnet via the selected provider; the same account shape detected on testnet via direct contract reads; provider outage demonstrably degrades to classic-only flow with a plain-language warning.
- Budget: $5,000
2. Protocol exit adapters: Blend, Aquarius, Soroswap
- Brief description: Per-protocol exits built client-side - Blend via @blend-capital/blend-sdk Pool.submit with repay-before-withdraw and health factor checks; Aquarius withdraw and claim; Soroswap remove_liquidity with client-side verification of API-built XDR - all behind a versioned wasmHash contract registry, satisfying the exit adapter invariants (live re-read, simulate before sign, clamp to balance, minimum-received bounds, no silent skips).
- How to measure completion: For each of the three protocols, a testnet account with an open position closes end to end; integration tests per protocol run in CI; an unknown wasmHash flags for manual review instead of building an exit.
- Budget: $12,000
3. Soroban conversion, allowance inspector, sponsored fees
- Brief description: Soroban token conversion through the Soroswap API (routing across the Soroswap Aggregator and the classic SDEX, with Stellar RPC as the live state source and Horizon for SDEX path-finding) with user-specified base asset support; the read-only allowance inspector (SEP-41 allowance discovery via getEvents plus a known-contract registry, one-click revocation); the CAP-15 fee-bump endpoint with shape validation, per-transaction fee caps, and rate limiting, so reserve-locked accounts can close.
- How to measure completion: A testnet account holding only Soroban tokens converts and closes; allowances listed and revoked on testnet; an account at exactly its minimum balance closes end to end with sponsored fees.
- Budget: $10,000
4. Adversarial test suite and threat model
- Brief description: Adversarial and edge-case test suite over hostile account states (sponsoring accounts, the 1000-subentry maximum, revoked trustlines, hash(x) and pre-auth signers, undercollateralized vaults, queued backstop withdrawals, lost confirmation responses); a written STRIDE threat model covering key handling, the two backend signing keys, transaction construction, and the session layer; a security tooling run with a remediation plan for critical, high, and medium findings.
- How to measure completion: Adversarial suite running in CI; threat model and tooling results with remediation plan published in the repository.
- Budget: $9,000
Tranche 3 (Deliverable Roadmap) - Mainnet
Tranche 3 - Production launch (Mainnet). Total: $48,000.
1. Security hardening and mainnet launch
- Brief description: Pre-launch security hardening across the signing paths, the mediator, and the fee-bump surface - the critical paths that handle user funds. This includes an independent security review through the SDF Audit Bank. All critical and high findings are resolved before launch. Mainnet deployment with CSP enforced (no unsafe-eval, no inline scripts), production monitoring, and alerting.
- How to measure completion: Independent security review complete with all critical and high findings resolved and documented; full wind-down including one DeFi exit executed on mainnet on the team's own account, linked from the explorer; production app live at
lumenwipe.com
 with CSP and monitoring confirmed.
- Budget: $20,000
2. Phoenix and FxDAO protocol exits
- Brief description: Exit adapters for Phoenix (withdraw_liquidity with unbond) and FxDAO (pay_debt plus collateral withdrawal), behind the same versioned wasmHash contract registry and satisfying the same exit adapter invariants as the T2 protocols. Shipped after security remediation so audit coverage applies to all five protocols at mainnet launch.
- How to measure completion: For each protocol, a testnet account with an open position closes end to end; integration tests run in CI; mainnet exit demonstrated on the team's own account, linked from the explorer.
- Budget: $6,000
3. Public REST API and TypeScript SDK
- Brief description: Read-only REST API (batch account analysis, per-step unsigned XDR generation) and the transaction builder published as a typed npm package, so wallets and platforms embed the wind-down with their own signers. Reference integration path for the signed Pollar partnership.
- How to measure completion: npm package published; API documented; an integration example closes a testnet account driven entirely through the SDK without the web UI.
- Budget: $14,000
4. Production UX and documentation
- Brief description: UX pass from moderated user testing of the irreversible-action flows (plan review, confirmations, failure recovery), plain-language error coverage, and complete public documentation including API and SDK integration guides.
- How to measure completion: Five recorded user-testing sessions with findings addressed; docs site covers the user flow, the REST API, and the SDK integration guide; all UI errors human-readable (no raw SDK codes), verified by the E2E suite.
- Budget: $8,000
Team
We are a team of two software engineers and active Stellar Ambassadors with a proven track record building dev tools and end-user apps on Stellar.
Sebastian Salazar
 | Ecosystem Engineering Lead -
LinkedIn
 |
GitHub
Founder of Acachete Labs and former Stellar Fellow at OnlyDust, Sebastian builds open-source developer tooling for Stellar and ZK cryptography. Projects include
Akkuea
 (RWA infrastructure on Stellar: 230+ forks, 200+ contributors) and
Cougr
 (ECS framework for on-chain games: 40+ forks, 130+ crate downloads), both featured on Drips. He won the ETH Pura Vida hackathon with Revolutionary Farmers, reaching 60+ contributors per repository.
Miguel Nieto
 | Core Systems Lead -
LinkedIn
 |
GitHub
Senior Software Engineer, Miguel is a two-time SCF Build Award recipient. He helped build the
Elixir Stellar SDK
 (+15k downloads) and
Chaincerts
 (decentralized identity and verifiable credentials platform). Other contributions:
soroban-did-contract
 (decentralized identifiers on Soroban),
Tippa
 (cascading donations on Stellar), and
StellarView
 (ecosystem explorer and analytics).
Sebastián Salazar
Miguel Nieto
```
