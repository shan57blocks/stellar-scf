# Authline -  Stellar asset onboarding: Authline - Trustline Onboarder & SDK

Source: https://communityfund.stellar.org/submissions/recVyBmi9h4ycsMy6 (SCF #44, awarded $75.0K, category: Developer Tooling)

- Website: https://testnet.authline.io/
- Architecture doc: https://docs.google.com/document/d/1qlIGWzJgLmgjEbMUbREtkkznDJ63WtN5/edit?usp=sharing&ouid=104309477609059973990&rtpof=true&sd=true

## Links in the submission

- https://testnet.authline.io/
- https://docs.google.com/document/d/1qlIGWzJgLmgjEbMUbREtkkznDJ63WtN5/edit?usp=sharing&ouid=104309477609059973990&rtpof=true&sd=true
- https://github.com/theahaco/authline
- http://testnet.authline.io
- https://stellar.expert/explorer/public/contract/CB2DHZMQHQE3TGUMD6BRM7UCJZNIPKDRVEQOWBIRRS3G2FZOGDTRKSB3
- https://testnet.authline.io
- http://github.com/theahaco/authline

## Submission text (as published)

```text
Products & Services
Before a user can receive or withdraw a Stellar classic asset, they need an authorized trustline for it. In every real third-party flow, an exchange paying out a stablecoin withdrawal, a broker settling a regulated euro token, the transfer simply fails or is blocked when that trustline doesn't exist. At best the user sees a jargon error telling them to open a "trustline"; often nothing works and nobody tells them why. (A signing prompt only appears on platforms that already integrated one.)
Fixing it is left to the user, alone, in their own wallet: find the right asset and issuer, fund a reserve they didn't know existed, sign an operation they don't understand. Most stall.
Issuers of regulated (AUTH_REQUIRED) assets carry the other half of the burden: they must authorize every new trustline before balances can move, today either by hand (SetTrustLineFlags, holder by holder) or by running a SEP-8 approval server that has to co-sign every transaction, forever.
Authline reads the problem the way the RFP states it: the main actor is a THIRD PARTY onboarding a user into an asset, usually during a withdrawal, not an end-user web page. One protocol rule, stated up front: creating a trustline (CHANGE_TRUST, or CAP-73 trust()) always needs the user's own signature, nobody can create one on a non-custodial account. So "open a trustline for a user" means the third party does everything else: it pays the 0.5 XLM reserve (CAP-33 sponsorship, including creating the account for brand-new users), authorizes on the issuer's behalf (anyone can trigger it, through the onchain Authorizer), and builds the transaction and hands it to the user's wallet. The user signs at most once, and zero times in the common case where an unauthorized trustline already exists.
The core design decision: the open-vs-regulated branch lives ONCHAIN, in one small router contract, onboard(sac, holder), two arguments, one signature. It creates the trustline (CAP-73), finds the asset's authorizer on the ledger (CAP-68 SAC.admin()), calls it in the same transaction when one exists, re-checks the ledger to confirm the result, and returns a clear status: Authorized or TrustlineOnly. The integrator's code never branches on asset type, and there is no authorizer address to fake. If the policy says no, the whole transaction rolls back, trustline included. Open assets (like USDC, EURC) the majority of classic assets, whose issuers never set AUTH_REQUIRED go through the same call and simply have nothing to authorize.
What we ship, and the Stellar features each piece uses:
Onboard router (Soroban contract): creates the trustline (CAP-73), finds the authorizer onchain (CAP-68), authorizes in the same transaction, and double-checks the result on the ledger. Stateless, no admin.
Trustline Authorizer, the reference implementation (Soroban contract) the issuer installs as its asset's SAC admin: anyone can request an authorization; the policy (denylist or allowlist) decides who gets through. Ban and freeze keep onboarding honest. Every decision is an onchain event. It's the asset-agnostic generalization of our live mainnet EURCV contract; the issuer's own compliance operations (pausing, upgrades, treasury) stay on the issuer's keys.
Integrator SDK (@theaha/authline, TypeScript): builders for the three onboarding transactions (router path; CAP-33 sponsored ChangeTrust; authorize-on-behalf), an activation-status check, wallet handoffs (SEP-7 link, deep link, hosted redirect), a pinned asset registry so a fake authorizer can't be swapped in, a small React layer, and the issuer admin CLI.
Hosted relayer (self-hostable Docker): GET /status + POST /authorize. About 20 lines to integrate, in any language, no Stellar SDK. Ships in Tranche 2.
Claimable-balance delivery: if the recipient isn't ready, the exchange sends a claimable balance instead of a payment (with a reclaim clause); the user later claims it in one signed transaction that also creates the trustline. Ships in Tranche 2.
A reference exchange-withdrawal demo on testnet, both asset types, fresh keypairs on every run so any reviewer can reproduce it.
Discovery: an issuer adds one [TRUSTLINE_ONBOARDER] block to its stellar.toml, and every integrator can find its onboarder. No one-to-one deals.
The Authline dApp (live at
testnet.authline.io
): the recipient-side activation page, multi-asset, works with Stellar Wallets Kit wallets, warns about clawback and freeze where relevant, the claim flow.
The integration ladder, what a platform actually has to do:
Tier 0, link out: add the activation page URL to the withdrawal error path. One URL template, zero code.
Tier 1, relayer: two HTTP calls before paying out. About 20 lines, any language; zero user signatures for users who already have a trustline.
Tier 2, claimable-balance delivery: swap Payment for CreateClaimableBalance when the destination lacks a trustline. 20-50 lines, same hot key; the withdrawal never fails.
Tier 3, full SDK: a sponsor key plus at most one in-flow signature, the best UX. The destination, not the entry fee.
Core mechanism: the RFP's option (a), "authorize trustlines on behalf of users via a standard interface", the SEP-defined Authorizer interface (installed as the asset's SAC admin), the router, and the authorize-on-behalf path. Option (c), claimable-balance delivery, ships on top as Tier 2, the RFP explicitly invites the alternative mechanisms as additions. Option (b), the temporary intermediate account, is documented with our reasoning in our technical architecture, not built.
Requested Budget
$75.0K
Traction Evidence
We designed and built EURCV's Stellar integration for Société Générale-Forge, so we know this token design from the inside: a regulated AUTH_REQUIRED asset whose every holder needs a trustline created AND authorized by the SAC-admin contract before balances can move. This design makes it possible for Société Générale to preemptively ban accounts by having a list of accounts that can't be authorized. That's why we shipped
https://eurcv.theaha.co
, capitalizing on CAP-73 to bundle trustline creation and authorization into a single transaction, one signature.
It has run on Stellar mainnet since December 2025; authorizer contract: CB2DHZMQHQE3TGUMD6BRM7UCJZNIPKDRVEQOWBIRRS3G2FZOGDTRKSB3, verify at
https://stellar.expert/explorer/public/contract/CB2DHZMQHQE3TGUMD6BRM7UCJZNIPKDRVEQOWBIRRS3G2FZOGDTRKSB3
. We are also working with BitPanda to improve the SG-Bitpanda CEX-withdrawal flow for EURCV, the precise third-party withdrawal scenario this RFP targets.
That mainnet system is the precursor. Authline is its asset-agnostic generalization, already live on testnet at
https://testnet.authline.io
 for anyone to try: open the app, connect a wallet (Nido, Freighter, xBull, LOBSTR, via Stellar Wallets Kit), and activate a regulated AUTH_REQUIRED asset end-to-end in one signature through the onboard router, on a registry-backed multi-asset directory (USDC, EURC, BENJI queued next).
The public repo,
github.com/theahaco/authline
 (Apache-2.0), holds in code today: the router and authorizer-stub contracts, the @theaha/authline SDK with unit tests, the multi-asset activation dApp, runnable exchange-withdrawal demos for both asset classes, and a real-testnet e2e suite (Node + Playwright). CI gates every PR.
Company traction.
 The Aha Company is a stellar integration partner (founded 2021, ~16 engineers across the USA / France / Brazil). We built the Stellar CLI, Scaffold Stellar (used by 40+ companies), and shipped SCF public goods including Moonlight, the Stellar Registry, and Stellar Scaffold. CTO Willem Wyndham co-authors the Contract Admin SEP (admin-sep; SDF discussion #1670) that underpins the Authorizer's admin surface.
Tranche 1 (Deliverable Roadmap) - MVP
[Deliverable 1]: SEP interface freeze + the onboard router contract on testnet (D1.1)
- Brief description:
 We freeze the SEP-XXXX (TBD) interface and merge it to the repo: the router's
onboard(sac, holder)
 call, the Authorizer interface, the
stellar.toml
 discovery block, and the three onboarding cases. Then we finish the router contract that implements it. The router creates the trustline (CAP-73), looks up the asset's authorizer onchain (CAP-68), calls it in the same transaction when one exists, and returns Authorized or TrustlineOnly. If the authorizer rejects, the whole transaction rolls back, trustline included. The contract is stateless and has no admin. We deploy it to testnet, pin its address in the SDK, and run both asset types through it: an
AUTH_REQUIRED
 test asset and an open asset (testnet USDC).
- How to measure completion:
 Contract CI tests covering: the success path; a rejected authorization (with the rollback checked); a failed post-check; and an asset with no authorizer. Router live on testnet; one onboarding of each asset type visible on Stellar Expert.
- Estimated date of completion:
 July 17, 2026
- Budget:
 $11,000
--
[Deliverable 2]: Integrator SDK core + the Authline dApp on testnet, both asset classes (D1.2)
- Brief description:
 The TypeScript SDK (@theaha/authline): functions to find an asset's onboarder, build the onboarding transaction, check whether an account is ready to receive an asset, and a small React layer for dApp builders. Asset and router addresses ship pinned in the SDK so a fake authorizer can't be swapped in. The Authline dApp (already live at
testnet.authline.io
): pick an asset, connect a wallet, activate it, and see plainly whether you ended up authorized or just have a trustline.
- How to measure completion:
 on the deployed dApp, a user activates testnet USDC and a regulated test asset end-to-end through the router. SDK published to npm (pre-1.0).
- Estimated date of completion:
 July 31, 2026
- Budget:
 $6,500
Tranche 2 (Deliverable Roadmap) - Testnet
[Deliverable 1]: The three onboarding cases + claimable-balance delivery (D2.1)
- Brief description:
 The SDK builds all three transaction shapes. Case A: the user already has an unauthorized trustline, and anyone can submit the authorization for them, zero user signatures. Case B: the user has nothing, maybe not even XLM; the platform sponsors the account creation and the trustline reserve (CAP-33), and the user signs once. Case C: the router path from Tranche
On top of that, claimable-balance delivery for exchanges: if the recipient isn't ready, the exchange sends a claimable balance instead of a payment, and the user later claims it on the activation page, that one claim signature also sets up the trustline. We also ship the wallet handoffs (SEP-7 link, deep link, hosted redirect) and a runnable exchange-withdrawal demo covering both asset types.
- How to measure completion:
 each case runs on testnet. The SEP-7 link opens and signs in a wallet (our own Nido as the reference; external wallets too where available). A full withdrawal-then-claim run on testnet with one user signature.
- Estimated date of completion:
 August 21, 2026
- Budget:
 $9,000
--
[Deliverable 2]: The asset-agnostic Trustline Authorizer + issuer admin CLI (D2.2)
- Brief description:
 The EURCV admin contract is set as a SAC's admin by the issuer and picks a policy: denylist (everyone allowed except banned addresses) or allowlist (nobody allowed except listed ones). It has the admin controls an issuer needs: ban an address before it ever creates a trustline, freeze a holder (and make sure they can't slip back in by deleting and recreating their trustline), pause everything, upgrade the contract, and optionally mint and claw back. Every action emits an onchain event, so the full authorization history is auditable from the ledger. The CLI wraps all of it: the issuer's ops person types commands instead of crafting Soroban calls. Deployed on testnet as admin of the test asset, replacing the Tranche-1 stub.
- How to measure completion:
 contract tests pass, including "a frozen account cannot get re-authorized" and "paused rejects everything". Live on testnet, exercised through the router and through the authorize-on-behalf path. CLI documented with a runbook.
- Estimated date of completion:
 September 4, 2026
- Budget:
 $10,500
--
[Deliverable 3]: Test suite, hosted relayer, MiCA design doc, SEP update (D2.3)
- Brief description:
 The test suite the RFP asks for: unit tests run in CI, plus an end-to-end suite that runs against real testnet, including a browser test that drives the deployed dApp the way a user would. The relayer: a small HTTP service with two endpoints ("is this account ready?" and "authorize this account") so an exchange can integrate in about 20 lines of any language, without touching a Stellar SDK. We host one; it also ships as a Docker image for self-hosting. A short design document on how the authorization model fits MiCA obligations: what gets recorded onchain, and why no personal data is involved anywhere. And we update the SEP with everything we learned building this.
- How to measure completion:
 CI green; the e2e suite runs against testnet; relayer live with both endpoints and a runbook; design doc published; the updated SEP in the repo and posted to the public discussion thread.
- Estimated date of completion:
 September 18, 2026
- Budget:
 $7,500
Tranche 3 (Deliverable Roadmap) - Mainnet
[Deliverable 1]: Mainnet deployment + audit fixes (D3.1)
- Brief description:
 We deploy the router to mainnet (one instance per network, address pinned in the SDK). We deploy the Authorizer as the admin of a real AUTH_REQUIRED asset on mainnet. The target is EURCV, replacing today's single-asset contract, but that swap touches Société Générale's production asset and needs their change window. If that window doesn't land inside the tranche, we issue our own
AUTH_REQUIRED
 asset on mainnet, prove the system on it, and do the EURCV migration right after as a committed follow-up. We also run an open asset (e.g. mainnet USDC) through the mainnet router. And we fix what the SCF audit finds.
- How to measure completion:
 one third-party onboarding of each asset type on mainnet, visible on Stellar Expert (the regulated one being EURCV or our own issuance). Critical and high audit findings fixed.
- Estimated date of completion:
 October 2, 2026
- Budget:
 $12,000
--
[Deliverable 2]: Adoption: one wallet + one exchange/broker (D3.2)
- Brief description:
 The RFP asks for one wallet and one exchange/broker actually using the standard. Wallet side: via Stellar Wallets Kit. Exchange side: Bitpanda, through the Société Générale–Bitpanda EURCV work we are already part of. Integration counts from Tier 0 up. Tier 0 means the platform sends users from its "withdrawal failed" screen to the activation page, which is exactly the redirect flow the RFP itself describes, and a platform can climb to the deeper tiers (relayer call, claimable balances, full SDK) from there.
- How to measure completion:
 two independent platforms integrated and documented at Tier 0 or higher, the wallet plus the exchange, or the wallet plus a second wallet if the exchange is blocked. Real onboardings through the standard on mainnet, visible on Stellar Expert.
- Estimated date of completion:
 October 23, 2026
- Budget:
 $9,500
--
[Deliverable 3]: SEP finalized + docs + 1.0 release + user testing (D3.3)
- Brief description:
 Everything the standard needs to outlive the grant: the final SEP revision submitted to stellar-protocol, an integration guide a platform can follow without talking to us, the SDK's 1.0 release, and SCF's user-testing round supported on our side.
- How to measure completion:
 SEP PR open on stellar-protocol with the public discussion thread active; per-tier integration guide and docs site live; SDK 1.0 tagged and on npm; test build delivered for SCF's user-testing program and findings answered when they come; 14 days of stable mainnet operation; completion report filed.
- Estimated date of completion:
 October 30, 2026
- Budget:
 $9,000
Team
The Aha Company builds institutional tokenization and stablecoin infrastructure on Stellar and contributes Stellar public goods.
Chad Ostrowski
 - CEO Web2 startup + consulting veteran who moved into Web3 in 2019 (Ethereum DAO dApps), joined NEAR in 2020, and co-founded The Aha Company in 2021. Has led the design of key Stellar smart-contract developer workflows.
Connect →
GitHub
 |
LinkedIn
Willem Wyndham
 - CTO Programming-languages and security specialist (PhD track, University of Maryland) turned blockchain-runtime engineer at NEAR. Deep Rust/WebAssembly expertise, directly relevant to the Soroban admin/SAC authorization contract, and leads Aha's technical architecture.
Connect →
GitHub
 |
LinkedIn
Fabricius Zatti
 - Principal Engineer 15 years' experience, including 5+ building Web3 for institutions (Central Bank of Indonesia, MoneyGram, IOG, the SDF). Deep Stellar and smart-contract expertise across EVM, NEAR, Solana, and Cardano; active in Linux Foundation Decentralized Trust.
Connect →
GitHub
 |
LinkedIn
Pamphile Roy (tupui)
 - Principal Engineer Software-supply-chain / SLSA specialist and maintainer of Tansu (onchain provenance and governance on Soroban); active in CAP/SEP discussions, relevant to authoring this RFP's standard.
Connect →
GitHub
 |
LinkedIn
Enzo Soyer
 - COO Engineering + business training (CentraleSupélec, ESSEC) with operator experience across Web3. Leads strategy, partnerships, and execution, including the EURCV / Société Générale-Forge engagement, the regulated-asset onboarding work most relevant to this RFP. Active SCF Build reviewer.
Enzo Soyer
Chad Ostrowski
Willem Neal Wyndham
```
