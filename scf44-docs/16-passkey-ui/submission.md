# Passkey UI: SoroPass: Passkey SDK & UI Components

Source: https://communityfund.stellar.org/submissions/recKoOv9KoxgFTDE8 (SCF #44, awarded $95.0K, category: Developer Tooling)

- Website: https://soropass.dev
- Architecture doc: https://soropass.notion.site

## Links in the submission

- https://soropass.notion.site
- https://github.com/justmert/soropass
- https://stellar.expert/explorer/testnet/tx/58cd5d6307acc61d830bdb8b3a7299761bcf6435ddf0065f652e635457f5cc60
- https://stellar.expert/explorer/testnet/tx/2a89cc6f38b2af4c59fbea37ac77e426a0092060a6be2842ec2cb6b0b9645a61
- https://stellar.expert/explorer/testnet/contract/CBDOCYVDUBEGLZKT6OFMXBFLY5MTVHHHA6X7ARWHHLLTPFT5FTE3DFQ7
- http://docs.soropass.dev/compatibility
- http://demo.soropass.dev
- http://docs.soropass.dev
- https://github.com/justmert/icp-agent-kit
- https://github.com/justmert/arbitrum-python-sdk
- http://docs.rs
- https://github.com/justmert/koios.rs
- https://github.com/justmert/timeboost-analytics

## Submission text (as published)

```text
Products & Services
SoroPass turns a device passkey into the signer of a Soroban smart account — create, sign, and recover with no seed phrase and no backend.
Verified end-to-end on Stellar testnet, by hand on macOS with Touch ID: create a smart wallet, deploy it on-chain, send a passkey-signed payment, and watch the same contract reject a forged signature.
Proof links from demo:
Passkey-signed payment:
https://stellar.expert/explorer/testnet/tx/58cd5d6307acc61d830bdb8b3a7299761bcf6435ddf0065f652e635457f5cc60
Forged signature rejected:
https://stellar.expert/explorer/testnet/tx/2a89cc6f38b2af4c59fbea37ac77e426a0092060a6be2842ec2cb6b0b9645a61
Smart-account contract:
https://stellar.expert/explorer/testnet/contract/CBDOCYVDUBEGLZKT6OFMXBFLY5MTVHHHA6X7ARWHHLLTPFT5FTE3DFQ7
@soropass/core — passkey SDK.
 ES256-only with always-low-S normalization (the edge cases that break ~half of Apple passkeys).
Stellar:
 assembles a SorobanAuthorizationEntry verified on-chain by Soroban's native secp256r1_verify (CAP-0051).
Impact:
 correct passkey→Soroban auth without rebuilding the crypto.
Smart accounts + recovery.
 A secp256r1 webauthn-account and a deterministic AccountFactory, kept OpenZeppelin-compatible.
Stellar:
 __check_auth makes the passkey the account's authority; recovery reads an on-chain event over public soroban-rpc, and a second passkey can be added on a new device.
Impact:
 non-custodial, no seed phrase, recoverable.
PasskeyModule for stellar-wallet-kit.
 Implements the kit's ModuleInterface (getAddress, signTransaction, signAuthEntry, isAvailable).
Stellar:
 passkey wallets appear in the standard wallet picker beside Freighter and Lobstr.
Impact:
 teams switch it on like any other wallet.
Compatibility guide + matrix (CI).
 A published guide of what works, what breaks, and the fallback per case, backed by a re-runnable harness (Playwright + Chrome DevTools virtual authenticators) and checked on real hardware (macOS / Touch ID).
Stellar:
 round-trips verify against the SDK's Soroban-bound p256 path.
Impact:
 wallets know what to ship; it stays current.
UI + adapters.
 Framework-agnostic create / sign / recover components themed via tokens.css; swappable submission (direct / Launchtube / OZ Relayer) and indexer (events / Mercury).
Stellar:
 defaults need only public soroban-rpc.
Impact:
 drop-in for any wallet, no lock-in or mandatory infrastructure.
Requested Budget
$95.0K
Traction Evidence
Since this is an RFP track build, so there isn't prior traction in the usual sense of users or transactions. The demand is already clear, since the Passkey UI RFP exists because multiple wallet teams need this and several are building passkey flows from scratch.
Ahead of this submission I've built a working proof of capability, including a minimal passkey SDK (ES256 only, with client side low-S normalization), a CI backed compatibility matrix, and a PasskeyModule running on a Stellar-Wallets-Kit branch with a testnet demo. Everything is public at
https://soropass.dev/
 and
https://github.com/justmert/soropass
.
Tranche 1 (Deliverable Roadmap) - MVP
Goal:
 Ship the compatibility guide and the SDK core. By the end of Tranche #1 the guide is published, the matrix re-runs in CI, and the SDK can create a passkey and produce a valid secp256r1 signature, all covered by tests.
Allocation:
 $19,000 (20%)
Deliverable 1 — Passkey compatibility guide + matrix + test harness
Brief description:
 A published guide covering passkeys on web (Chrome, Safari, Firefox, Edge), with mobile guidance where support differs. For each case it states what works, what fails, the fallback, and the matching SDK behavior, with a source on every entry. The findings live as structured data that the SDK also reads for its runtime fallbacks, so the guide and the code stay in sync. A re-runnable harness (Playwright + Chrome DevTools Protocol virtual authenticators) and live feature-detection produce the automated rows; real-device sessions for web/mac/android.
How to measure completion:
 Guide live at
docs.soropass.dev/compatibility
 and matrix.
<date>
.json committed to the repo. Any row re-runs with
pnpm matrix:ci
 and CI passes. At least one real-device session (macOS / Touch ID) logged with its date and result.
Budget:
 $11,000
Deliverable 2 — @soropass/core SDK: create + sign
Brief description:
 The SDK core: ES256-only passkey registration, DER→compact low-S signature conversion, the WebAuthn signing digest, public-key extraction (COSE and SEC-1), deterministic wallet-address derivation, and a typed error for each failure case.
How to measure completion:
 Package builds to ESM, CJS, and types.
pnpm test
 passes on Node 20 and 22, including a test that a high-S signature is converted to low-S and a test that a non-ES256 credential is rejected, checked against Node's crypto and the Stellar SDK's own preimage construction.
Budget:
 $8,000
Tranche 2 (Deliverable Roadmap) - Testnet
Goal:
 Complete the SDK and the UI components, add on-chain multi-device recovery, and prove the whole flow on testnet with a live demo and docs.
Allocation:
 $28,500 (30%)
Deliverable 1 — Soroban signing, multi-device recovery, and adapters
Brief description:
 Build the Soroban authorization entry with the challenge bound to the auth preimage and encode the signature in the exact shape the contract's __check_auth reads. Add connect and recover, including adding a new passkey signer from a new device on-chain (multi-signer add_signer) so a user is not locked out if a device is lost. Pluggable submission (direct, Launchtube, OpenZeppelin Relayer) and indexer (events, Mercury) adapters; direct and events are the defaults and need no extra service.
How to measure completion:
 The test suite passes across the matrix cases and the assembled entry verifies against the contract's __check_auth; the add-a-second-device flow runs on testnet (TX on Stellar Expert). A build smoke test confirms @stellar/stellar-sdk is a peer dependency and is not bundled.
Budget:
 $12,000
Deliverable 2 — UI components: create, sign, recover, add-device
Brief description:
 Create, sign, recover, and add-device components that work with any framework: a headless layer (logic, accessibility, translations) and a styled layer themed through tokens.css, usable without adopting a design system. The framework choice is documented with reasons.
How to measure completion:
 UI tests pass; the demo shows every state; re-theming works by swapping tokens.css (screenshots in light and dark); the framework-decision document is published.
Budget:
 $9,000
Deliverable 3 — Testnet demo + developer docs site
Brief description:
 A demo wallet on testnet — create a passkey account, deploy it, send a payment signed by the passkey, show that a wrong key is rejected, and recover on a second device — plus a docs site (getting started, API reference, integration guide) and a short screen recording.
How to measure completion:
 Demo live at
demo.soropass.dev
 with a full create → payment → wrong-key rejection → second-device recovery run, all TX hashes on Stellar Expert;
docs.soropass.dev
 live; a recording (≤90s) published.
Budget:
 $7,500
Tranche 3 (Deliverable Roadmap) - Mainnet
Goal:
 Ship to mainnet, take the PasskeyModule to stellar-wallet-kit, and publish the docs and agent skill.
Allocation:
 $47,500 (50%)
Deliverable 1 — PasskeyModule pull request to stellar-wallet-kit
Brief description:
 A PasskeyModule implementing the kit's ModuleInterface (getAddress, signTransaction, signAuthEntry, isAvailable), built into the official Creit-Tech/Stellar-Wallets-Kit codebase and opened as a pull request to that repo — not shipped as a separate package — with a reference app that registers passkey wallets in the kit's own wallet picker next to Freighter and Lobstr. Following confirmation of the interface with the Creit Tech maintainers; the PR is opened and pending their merge.
How to measure completion:
 PR opened on the official Creit-Tech/Stellar-Wallets-Kit repo; the reference app runs the module inside the real Stellar Wallets Kit modal and signs a transaction (recording).
Budget:
 $18,000
Deliverable 2 — Mainnet deployment
Brief description:
 Deploy the webauthn-account and AccountFactory contracts to mainnet, publish @soropass/core and @soropass/ui at 1.0 (Apache-2.0), and run the demo on mainnet with a passkey-created account, a passkey-signed payment, and recovery on a second device.
How to measure completion:
 Mainnet contract IDs and a passkey-signed mainnet transaction on Stellar Expert; the 1.0 packages published and installable from npm.
Budget:
 $17,000
Deliverable 3 — Developer docs + agent skill
Brief description:
 The docs site at
docs.soropass.dev
 completed with the full API reference and a wallet integration guide, plus a machine-readable skill file at
soropass.dev/skill.md
 that lets an AI agent integrate the SDK (create, sign, recover, kit module) without extra instructions.
How to measure completion:
docs.soropass.dev
 live with the API reference;
soropass.dev/skill.md
 resolves; an AI agent reproduces a create + sign using only the skill file.
Budget:
 $12,500
Team
Mert Köklü - Software Engineer
Mert Köklü is a software engineer with over four years of blockchain experience, focused on developer tooling, SDKs, and integrations. He holds a Bachelor's in Computer Science and has led engineering teams across blockchain and AI. He is the principal engineer and sole developer on this project, available full-time for the duration of the engagement. His work centers on building well-documented SDKs that other developers adopt, and he has worked with passkey-based authentication in the Internet Computer ecosystem, whose Internet Identity is built on the WebAuthn passkey standard. He has also shipped Stellar/Soroban tooling and several published SDKs across ecosystems.
Projects
SoroReveal:
 Decompiles Soroban smart contracts back to readable source (
https://sororeveal.com/
)
ICP Agent Kit:
 SDK for natural-language interaction with the Internet Computer; hands-on work with passkeys, since ICP's Internet Identity is built on the WebAuthn passkey standard (
https://github.com/justmert/icp-agent-kit
)
Arbitrum Python SDK:
 the leading community Python SDK for Arbitrum (
https://github.com/justmert/arbitrum-python-sdk
)
koios.rs
:
 published Apache-2.0 typed async Rust SDK for the Koios Cardano API, with rate limiting, a typed error model, green CI, and
docs.rs
 docs (
https://github.com/justmert/koios.rs
)
Timeboost Analytics:
 Analytics engine for Arbitrum's Timeboost express-lane auctions (
https://github.com/justmert/timeboost-analytics
)
Profiles and Contact
GitHub:
https://github.com/justmert
LinkedIn:
https://www.linkedin.com/in/mertkoklu
Telegram: @mertkklu
Mert Köklü
```
