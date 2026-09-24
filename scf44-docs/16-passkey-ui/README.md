# Passkey UI (SoroPass)

SoroPass is a toolkit for Stellar wallet teams. It lets a user control a Soroban smart account with a device passkey instead of a seed phrase. A passkey is the Face ID, Touch ID, or security-key login built into phones and laptops. A smart account is a wallet that is itself a Soroban contract (Soroban is Stellar's smart-contract system). SoroPass has four parts: a code library (SDK), ready-made screens, a plug-in for the common Stellar Wallets Kit, and a guide to which browsers and devices support passkeys. It is built for the SCF "Passkey UI" RFP (a request for proposals where SCF names a tool it wants built). One developer, Mert Köklü, builds it.

## What SCF #44 pays them to build

Total award: $95,000.

- **Tranche 1 — MVP ($19,000)**
  - Compatibility guide, test harness, and matrix ($11,000). The guide lists what works and what breaks on Chrome, Safari, Firefox, and Edge, plus mobile notes. It is published at docs.soropass.dev/compatibility and re-runs in CI (automated tests on each change).
  - `@soropass/core` SDK, create and sign ($8,000). It accepts only one passkey signature type (ES256) and always fixes signatures into "low-S" form, a format rule the chain requires. It has tests on Node 20 and 22.
- **Tranche 2 — Testnet ($28,500).** Testnet is Stellar's practice network.
  - Soroban signing, recovery on a second device, and swappable "adapters" ($12,000). One adapter type sends transactions: direct, Launchtube, or OpenZeppelin Relayer. The other looks up accounts: events or Mercury.
  - UI components for create, sign, recover, and add-device, working with any web framework and themed with `tokens.css` ($9,000).
  - Testnet demo at demo.soropass.dev, a docs site at docs.soropass.dev, and a screen recording of 90 seconds or less ($7,500).
- **Tranche 3 — Mainnet ($47,500).** Mainnet is the real network.
  - A `PasskeyModule` pull request to the official Creit-Tech/Stellar-Wallets-Kit repo, with a reference app ($18,000).
  - Contracts on mainnet, plus `@soropass/core` and `@soropass/ui` published at version 1.0 under the Apache-2.0 license ($17,000).
  - Full developer docs, plus `soropass.dev/skill.md`: a file an AI agent can read to use the SDK ($12,500).

## How it works

- **No SoroPass server.** Everything runs in the user's browser and talks to public soroban-rpc (Stellar's public contract API).
- **Passkey signs, contract checks.** The SDK builds the Soroban authorization for a transaction. It uses that authorization's hash as the passkey "challenge" (the data the passkey signs), so each signature covers exactly one transaction. The passkey signs it, and the SDK converts the signature to low-S form. On-chain, the account contract's `__check_auth` function checks it with Stellar's built-in secp256r1 check. That check was added by CAP-51 in Protocol 21. secp256r1 is the curve passkeys use.
- **Contracts.** There are two:
  - `webauthn-account`: the smart account itself.
  - `AccountFactory`: creates one account per passkey at a predictable address. It then publishes an "event" (a public on-chain log entry).
- **Recovery.** To reconnect on any device, the SDK reads that public event to find the account again, with no database. A second passkey can be added as an extra signer.
- **Wallet plug-in.** `PasskeyModule` puts passkey wallets in the Stellar Wallets Kit picker, next to Freighter and Lobstr.
- **Compatibility matrix.** It merges three sources: MDN browser data, automated browser tests with virtual passkeys, and live feature checks. It refreshes weekly. Chromium is machine-tested. Firefox and Safari are checked by hand.
- **Stellar features used:** Soroban custom accounts (`__check_auth`), CAP-51 secp256r1, SEP-41 tokens (the native XLM token contract), and Soroban events.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recKoOv9KoxgFTDE8 | submission.md | The only submission on the project page, so no earlier rounds |
| SoroPass Technical Architecture (Notion) | architecture | https://soropass.notion.site | architecture.md | Five diagrams are images, so only their captions are kept. The source files are in the repo's `docs/architecture/diagrams/` |
| Repo README | docs | https://github.com/justmert/soropass | repo-readme.md | Install, quick start, and testnet proof links |
| RFP submission pack (in repo) | pitch / planning | https://github.com/justmert/soropass/blob/main/docs/rfp/submission-pack.md | rfp-submission-pack.md | Titled "SCF #43", with a $100,000 / 4-tranche plan that differs from the SCF #44 award. No SCF #43 submission is listed on the project page |
| Threat model | spec / security | https://github.com/justmert/soropass/blob/main/docs/security/threat-model.md | threat-model.md | Written for the SCF Audit Bank. Not an audit |
| Compatibility guide | spec | https://github.com/justmert/soropass/blob/main/docs/compatibility.md | compatibility-guide.md | Tranche 1 deliverable |
| UI framework decision | architecture | https://github.com/justmert/soropass/blob/main/docs/ui/framework-decision.md | ui-framework-decision.md | Tranche 2 deliverable |
| Stellar Wallets Kit integration design | architecture | https://github.com/justmert/soropass/blob/main/docs/integration/stellar-wallets-kit.md | wallets-kit-integration.md | |
| Contracts README | spec | https://github.com/justmert/soropass/blob/main/contracts/README.md | contracts-readme.md | |
| Docs site | docs site | https://docs.soropass.dev/docs | — | Live |
| Compatibility matrix (live) | docs site | https://docs.soropass.dev/docs/compatibility | — | The submission's `docs.soropass.dev/compatibility` link redirects here |
| Website / live demo | demo | https://soropass.dev | — | Live |
| Testnet demo | demo | https://demo.soropass.dev | — | Live |
| Agent skill file | docs | https://soropass.dev/skill.md | — | Live, about 29 KB. A Tranche 3 deliverable, already published |
| Upstream wallet-kit pull request | evidence | https://github.com/Creit-Tech/Stellar-Wallets-Kit/pull/112 | — | Open, not yet merged |
| Wallet-kit proposal issue | evidence | https://github.com/Creit-Tech/Stellar-Wallets-Kit/issues/95 | — | |
| passkey-kit alignment issue | evidence | https://github.com/kalepail/passkey-kit/issues/32 | — | |
| Passkey-signed payment (testnet) | demo | https://stellar.expert/explorer/testnet/tx/58cd5d6307acc61d830bdb8b3a7299761bcf6435ddf0065f652e635457f5cc60 | — | Stellar Expert loads the details with JavaScript |
| Forged signature rejected (testnet) | demo | https://stellar.expert/explorer/testnet/tx/2a89cc6f38b2af4c59fbea37ac77e426a0092060a6be2842ec2cb6b0b9645a61 | — | Same |
| Smart-account contract (testnet) | demo | https://stellar.expert/explorer/testnet/contract/CBDOCYVDUBEGLZKT6OFMXBFLY5MTVHHHA6X7ARWHHLLTPFT5FTE3DFQ7 | — | Same |
| npm package `@soropass/core` | docs | https://registry.npmjs.org/@soropass/core | — | Latest version 0.3.1. `@soropass/ui` is at 0.3.0. The npmjs.com page blocks scripts (403) |

## Gaps

- `https://github.com/justmert/passkey-ui` returns 404, so it is private or deleted. The SCF page lists it among the team's links.
- There is no pitch deck, whitepaper, or independent audit. Only the team's own threat model exists.
- The Tranche 2 screen recording (90 seconds or less) was not found in the repo or docs.
- The Notion architecture diagrams are images and were not transcribed. The same diagrams are in the repo as HTML/PNG files.
- The repo's "submission pack" describes an SCF #43 bid ($100,000). The SCF project page shows no SCF #43 submission for this project.
