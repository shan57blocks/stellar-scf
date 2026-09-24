Source: https://soropass.notion.site (public Notion page 37f333f48e4080b7a819d214e12aef6f)

(Exported from the public Notion page. The five diagrams are images and are not included; their captions are kept.)

Soropass - Technical Architecture 

> **Project:** Soropass — Passkeys as native signers for Stellar smart accounts

> **Track:** SCF #44** - **SCF Build Award · RFP Track · Passkey UI

> **Integrates with:** @[creit.tech/stellar-wallets-kit](http://creit.tech/stellar-wallets-kit) · OpenZeppelin Soroban Smart Accounts · 
WebAuthn / passkeys · soroban-rpc

> **Repo:** [github.com/justmert/soropass](https://github.com/justmert/soropass) · **Site:** [soropass.dev](https://soropass.dev) · **Docs:** [docs.soropass.dev](https://docs.soropass.dev) · 

This document describes how Soropass turns a device passkey into the signer of a Soroban smart account, how the SDK is adopted into stellar-wallets-kit, and how the compatibility matrix is kept honest and current. The focus is architecture: how the system works end-to-end, and what is already proven on Stellar testnet.



---

## 1. System Overview

**Wallet teams keep rebuilding passkey authentication from scratch**, and passkey behavior across devices, browsers, and hardware is a minefield. **Soropass collapses this into a minimal, composable layer:** an ES256-only passkey SDK, reference UI, a drop-in module for the standard wallet kit, and a living compatibility matrix,** so a wallet can offer seed-phrase-free, non-custodial smart accounts in an afternoon.**

| Task for a wallet team | Today (from scratch) | With Soropass |
|---|---|---|
| Add passkey login | Hand-build WebAuthn + Soroban auth, handle Apple high-S, ES256 quirks | Drop in @soropass/core; ES256 + low-S enforced for you |
| Know what works where | Manually test devices, guess at fallbacks | Read the dated, diffable compatibility matrix |
| Sign a Soroban transaction | Hand-assemble the auth entry to match the contract's signature shape | signTransaction() / signAuthEntry() emit the verified shape |
| Account recovery | Run an indexer / database mapping credential → address | recover() reads a public on-chain event — zero-infra |
| Custody | Custodial, or a seed phrase the user must keep | Non-custodial smart account; key stays in the secure element |
| Ship the UI | Build create / sign / recover screens + accessibility | Drop-in headless or token-themeable styled components |

**Why Stellar.** Stellar is the only chain where this design is both simple and cheap. CAP-51 (Protocol 21, June 2024) added host-native secp256r1 verification, so a P-256 passkey verifies on-chain without the expensive in-contract elliptic-curve code or precompile workarounds other ecosystems require. Soroban's custom-account model (`__check_auth`) lets that passkey be the account's authority directly — not a wrapper around an external key. Sub-cent fees and 3–5s finality make per-user smart-account deployment and on-chain credential→address recovery economically rational. Every layer of Soropass is calibrated to Stellar's secp256r1 host function, Soroban auth framework, and cost profile; it would not port cleanly to a chain without them.

> 

[image: attachment:b711e2f9-de6a-4970-8114-5d256a57cdfd:soropass-1-system-architecture.png]

System architecture — the Soropass client (UI, PasskeyModule, core SDK) turns a device passkey into a Soroban authorization. All on-chain operations flow through public soroban-rpc to the AccountFactory, the webauthn-account smart account, and the native XLM SAC. No Soropass backend sits in the path.

---

## 2. @soropass/core — Passkey SDK & On-Chain Authorization

**Package:** `@soropass/core` (TypeScript, ESM + CJS + types)

**Integration files:** `src/webauthn/*`, `src/soroban/{preimage,assemble,sign,checkAuth}.ts`, `src/anchors.ts`

### 2.1 Two non-negotiable invariants

- **ES256-only.** Registration offers exactly one algorithm; anything else hard-fails. Soroban verifies secp256r1 (P-256) only.
- **Always low-S.** WebAuthn signatures are DER and ~50% of Apple passkeys are high-S; the host `secp256r1_verify` does not reject high-S, so the SDK normalizes client-side before submission — proven by a unit test that feeds a real high-S signature and asserts low-S out.
Errors are a closed `KitError` union of 10 codes (`USER_CANCELLED`, `ES256_NOT_SUPPORTED`, `RP_ID_MISMATCH`, `ORIGIN_MISMATCH`, `CHALLENGE_MISMATCH`, `INVALID_SIGNATURE_DER`, `INVALID_PUBLIC_KEY`, `CONTRACT_AUTH_FAILED`, `NETWORK_ERROR`, `UNSUPPORTED_AUTHENTICATOR`).

```
// ES256-only: the SDK offers exactly one algorithm at registration
pubKeyCredParams: [{ type: 'public-key', alg: -7 }]
// a non-ES256 credential (e.g. RS256, -257) → KitError('ES256_NOT_SUPPORTED')

// sign a Soroban auth with a passkey — assertion → low-S → Secp256r1Signature ScVal, in one call
const signedXdr = await signTransaction(txXdr, {
  networkPassphrase,   // bound into the challenge preimage
  sign: passkeySigner, // navigator.credentials.get under the hood
})
```

> 

[image: attachment:5ee2a3ef-3e06-4f1e-86be-091cce0db4ce:soropass-2-passkey-auth-flow.png]

Passkey authorization flow — six steps from building the transaction to on-chain verification, with the challenge bound to the Soroban auth preimage and the signature normalized to low-S. Correct key succeeds; a wrong key traps in secp256r1_verify.

### 2.2 The six steps

1. **Build** — the app constructs the transaction or auth entry to authorize.
1. **Bind the challenge** — `challenge = SHA256(XDR(HashIdPreimage::SorobanAuthorization{ networkId, nonce, signatureExpirationLedger, invocation }))`, `networkId = SHA256(passphrase)`. This binds the passkey to this auth — replay-safe by construction.
1. **Assert** — `navigator.credentials.get(challenge)` returns `authenticatorData`, `clientDataJSON`, and a DER signature; the private key never leaves the secure element.
1. **Normalize + pack** — DER → compact r‖s, low-S normalize, then pack `{ authenticator_data, client_data_json, signature }` as a `Secp256r1Signature` ScVal into the entry.
1. **Submit** — a submission adapter sends the signed `SorobanAuthorizationEntry` via soroban-rpc.
1. **Verify on-chain** — the smart account's `__check_auth` runs the WebAuthn check (below).
### 2.3 What the contract verifies (Rust, `contracts/webauthn-account`)

```
// 1. bind the assertion to THIS Soroban auth: clientDataJSON.challenge == base64url(signature_payload)
let expected = base64url_nopad(&e, &payload_bytes);
if !client_data_challenge_matches(&e, &signature.client_data_json, &expected) {
    return Err(Error::ChallengeMismatch);
}
// 2. message = authenticator_data ‖ SHA256(client_data_json)
let mut message = signature.authenticator_data.clone();
message.append(&e.crypto().sha256(&signature.client_data_json).to_bytes().into());
// 3. host-native P-256 verify over SHA256(message); traps on a bad signature
e.crypto().secp256r1_verify(&public_key, &e.crypto().sha256(&message), &signature.signature);
```

The `__check_auth` algorithm mirrors the audited OpenZeppelin / kalepail WebAuthn verifier and uses the standard `CustomAccountInterface`, so the same SDK output verifies against production OZ Smart Accounts. Multi-signer wallets key the same struct by signer id in a Map — a small per-contract wrapper, not a different SDK.

---

## 3. Smart-Account Contracts & Account Lifecycle

**Contracts:** `contracts/webauthn-account` (single-signer secp256r1 account), `contracts/account-factory` (deterministic deployer + discovery event)

### 3.1 Why a factory + events

A passkey should map to a stable address with no custodial lookup service. The factory deploys each account deterministically (salt = SHA256(credentialId)), so one passkey ⇄ one C-address, and emits a discovery event. Recovery is then just reading that public event over RPC — no Soropass database holds the mapping.

> 

[image: attachment:395d4831-5826-4403-b6bf-9e02c8e9bdae:soropass-3-account-lifecycle.png]

Account creation & discovery — a new passkey deploys a deterministic smart account via the factory and emits a discovery event; reconnecting re-derives the same address by reading that public event. No custodial mapping store.

### 3.2 The lifecycle

1. `createPasskey()` → an ES256 credential (`credentialId` + P-256 public key, SEC-1, 65 bytes).
1. `AccountFactory.deploy(public_key, credential_id)` → a deterministic C-address; the contract publishes `("deployed", credential_id) → address`.
1. To reconnect on any device, `connect()` / `recover()` get a discoverable assertion, and `eventsIndexer.resolveByCredential(id)` reads `soroban-rpc getEvents` to re-derive the same address.
### 3.3 Deployed on testnet

- AccountFactory — `[CBVGSJEI…676TM](https://stellar.expert/explorer/testnet/contract/CBVGSJEIKGQ6MYFOWCBNV2NLLPJJV757UP6QQV6FDTI4S3N72OZ676TM)`
- webauthn-account, proven `__check_auth` — `[CB3IBD2J…6XB2](https://stellar.expert/explorer/testnet/contract/CB3IBD2JTLOFPLT4JFJ3KOIALDTKUMGLSSDZTOK7W2YWCZTPTZU66XB2)`
- webauthn-account, create-from-scratch — `[CDGZK67T…J7VQ](https://stellar.expert/explorer/testnet/contract/CDGZK67TRXJTSQL36PBBUIBKBULTGROX3ZIWVNHAY6DWEIATO2HZJ7VQ)`
- smart-account wallet, transfer demo — `[CBDOCYVD…DFQ7](https://stellar.expert/explorer/testnet/contract/CBDOCYVDUBEGLZKT6OFMXBFLY5MTVHHHA6X7ARWHHLLTPFT5FTE3DFQ7)`
- native XLM SAC — `[CDLZFC3S…CYSC](https://stellar.expert/explorer/testnet/contract/CDLZFC3SYJYDZT7K67VZ75HPJVIEUVNIXF47ZG2FB2RMQQVU2HHGCYSC)`
---

## 4. Proven on Stellar Testnet (real, not mocked)

> 

Every transaction below is a **live testnet transaction**, re-verified on Horizon at time of writing. Positive cases succeed; **wrong-key negative cases fail on-chain** — i.e. the contract really rejects a forged signature. This closes the gap between "the crypto verifies in a JS model" and "the Stellar network accepts it."

Network: `Test SDF Network ; September 2015` · RPC `https://soroban-testnet.stellar.org`

| Flow proven end-to-end on testnet | Correct key | Wrong key |
|---|---|---|
| Factory deploys a smart account + emits the indexer event | SUCCESS (Click) | — |
| Passkey assertion accepted by a deployed secp256r1 __check_auth | SUCCESS (Click) | trap (see negatives below) |
| Create-from-scratch: fresh passkey → factory.deploy → sign → __check_auth | SUCCESS (Click) | FAILED (Click) |
| Passkey-authorized 25 XLM transfer via the native SAC | SUCCESS (Click) | FAILED (Click) |

All runs are reproducible from the repo (`scripts/onchain-e2e.ts`, `factory-e2e.ts`, `transfer-e2e.ts`). One Soroban gotcha integrators must know: `simulateTransaction` records the required auth but does not execute `secp256r1_verify`, so it under-budgets CPU instructions and a naive submit fails with `ExceededLimit`; inflate the instruction budget before submitting (production submitters like Launchtube / OZ Relayer handle this).

---

## 5. Integration into stellar-wallets-kit

**Package:** `@soropass/wallets-kit-module` → a `PasskeyModule` implementing the kit's `ModuleInterface` as a thin adapter over `@soropass/core` (no crypto duplicated).

```
class PasskeyModule implements ModuleInterface {
  getAddress()      // → { address: 'C…' }  the smart-account contract id (via connect())
  signTransaction() // → core signTransaction (low-S, assembled entry)
  signAuthEntry()   // → core signAuthEntry
  isAvailable()     // → isUVPAA() within a 500 ms budget; never throws
  createAccount()   // → core createPasskey()  (beyond the base interface)
}
```

**Status (honest).** The module is built and **unit-tested against the kit's v2.x **`**ModuleInterface**` — conformance checked, the 500 ms budget proven (measured ~502 ms when isUVPAA hangs), and assembled auth entries pass a reference `__check_auth`. It is staged as a drop-in for the kit's `src/modules/`; adoption lands as an **upstream PR to **`**Creit-Tech/Stellar-Wallets-Kit**` — that PR is the integration milestone (planned, coordinated with the kit maintainers), exactly as the RFP requires: integration into the kit, not a parallel package.

---

## 6. Living Compatibility-Matrix CI

> 

[image: attachment:3579907b-a32c-433c-8113-26c83f68f9b0:soropass-4-matrix-ci-pipeline.png]

Living compatibility-matrix CI — three sources (MDN BCD, virtual-authenticator round-trips, live feature-detection) merge into one typed, dated, diffable snapshot, auto-refreshed weekly. Chromium is machine-verified; Firefox/Safari are Tier-2 manual.

The highest-value deliverable. Three sources merge into one zod-typed snapshot: MDN `browser-compat-data` for baseline support; a Playwright + Chrome DevTools Protocol harness that runs a real `create → get → verify` grid (transport × residentKey × userVerification) against virtual authenticators with the actual `@soropass/core` p256 path; and live feature-detection (`isUVPAA`, conditional mediation, `getClientCapabilities`). Snapshots are dated (`matrix.<date>.json`), diffed on substance (status/source/tier, ignoring timestamps), and a weekly cron (`0 6 * * 1`) plus push-to-main opens an auto-refresh PR. A hard CI gate fails the build if the canonical round-trip does not verify with `alg === -7`.

**Honest coverage:** Chromium is machine-verified every run; Edge is wired but best-effort; Firefox and Safari/WebKit are Tier-2 manual today (automation feasible via geckodriver / safaridriver, documented but not yet wired) — and clearly labelled so wallets know what is machine-checked vs. tracked-from-source.

---

## 7. UI Layer — framework-agnostic, drop-in

- **Headless primitives** (`@soropass/ui/headless`) — create / sign / recover / add-device state machines with ARIA prop-getters and i18n keys, zero DOM and zero framework dependency.
- **Styled reference** (`@soropass/ui/styled`) — vanilla-DOM `mount*Screen()` components, fully re-themeable via `tokens.css` (light / dark / brand / radius / RTL / reduced-motion) so wallets adopt them without inheriting a design system.
- **Reference demo** (`apps/demo`) — exercises every state in mock mode (zero network) and webauthn mode (real authenticator), plus an `/embed` target for docs live-previews and an on-chain page that does a real testnet round-trip.
The framework decision (framework-agnostic at every layer) is documented with rationale in `docs/ui/framework-decision.md`, honoring the RFP's "no bundled opinions on framework."

---

## 8. Stellar SEPs; Soroban Usage

| SEP / CAP | Role in Soropass |
|---|---|
| CAP-51 — secp256r1 verification | The Soroban host-native P-256 verify (Protocol 21). It is what lets a WebAuthn passkey be a first-class Stellar signer — the foundation of the whole design. |
| Soroban authorization framework (CustomAccountInterface / __check_auth) | webauthn-account implements __check_auth; the host calls it for every require_auth on the account. The SDK assembles the matching SorobanAuthorizationEntry, challenge bound to the HashIdPreimage::SorobanAuthorization preimage. |
| SEP-41 — Token Interface | The native XLM Stellar Asset Contract implements the SEP-41 token interface; passkey-authorized value transfers move through it. |
| Soroban contract events | The factory emits a deployed event; the default indexer resolves credentialId → address by reading it over public RPC. No off-chain index required. |

**Soroban interaction pattern.** Every state-changing call follows the canonical pattern: build the envelope, `simulateTransaction` for auth + resources, rebuild with the passkey signature (budget inflated for `__check_auth`), submit and poll to finality. Reads (events, balances) use public soroban-rpc directly.

---

## 9. Security Architecture

> 

[image: attachment:e76acdb1-f9f4-440b-87b5-098195974d2e:soropass-5-trust-boundaries.png]

Trust boundaries — four zones with a clear separation of what each holds. A breach of any single zone does not put user funds at risk: keys live only in the device secure element, funds move only with an on-chain secp256r1 signature.

- **Key management.** The passkey private key is generated and stored by the platform authenticator and never leaves the secure element. No seed phrase exists anywhere — there is no export or backup surface. Recovery uses discoverable credentials + the on-chain event; multi-signer accounts support add-device / backup-passkey flows.
- **Transaction safety.** The challenge is the Soroban auth preimage, so a signature authorizes exactly one transaction (replay-safe). Signatures are always low-S normalized. The client verifies origin, RP-ID, and challenge before assembling, mapping each failure to a typed `KitError`.
- **No custodial surface.** The default path (`direct` submission + `events` indexer) needs only public soroban-rpc — no Soropass server, API key, or database in the auth path. Soropass is never in a position to move funds. Full threat model in `docs/security/threat-model.md`.
---

## 10. Tech Stack

| Layer | Technology |
|---|---|
| SDK language | TypeScript — ESM + CJS + d.ts via tsup |
| Crypto | @noble/curves (p256) + @noble/hashes — the only runtime dependencies |
| Stellar | @stellar/stellar-sdk (peer dependency, never bundled), soroban-rpc, XDR |
| Contracts | Rust / Soroban — CustomAccountInterface, host secp256r1_verify |
| Wallet integration | @creit.tech/stellar-wallets-kit ModuleInterface (v2.x) |
| UI | Framework-agnostic headless + vanilla-DOM styled, token-themeable |
| Matrix CI | Playwright + Chrome DevTools Protocol, @mdn/browser-compat-data, zod |
| Tooling | pnpm monorepo, vitest, GitHub Actions (Node 20/22), Changesets |
| License | Apache-2.0 |

---

## 11. Repository Map

```
soropass/
├── packages/
│   ├── core/                     # @soropass/core — the passkey SDK
│   │   ├── src/
│   │   │   ├── webauthn/         # DER→raw, low-S, payload, clientData/authData parsers
│   │   │   ├── soroban/          # preimage · assemble · sign · checkAuth
│   │   │   ├── adapters/         # direct · launchtube · ozRelayer · events · mercury · factory
│   │   │   ├── ceremonies/       # create · connect · recover · browser client/signer
│   │   │   ├── anchors.ts        # ES256 + low-S + user-gesture battle-tested anchors
│   │   │   ├── errors.ts         # KitError taxonomy
│   │   │   └── testing/          # deterministic mock authenticator
│   │   └── scripts/              # onchain-e2e · factory-e2e · transfer-e2e (testnet proofs)
│   ├── ui/                       # @soropass/ui — headless + styled (create/sign/recover)
│   └── kit-module/               # PasskeyModule for @creit.tech/stellar-wallets-kit
├── contracts/
│   ├── webauthn-account/         # secp256r1 smart account (__check_auth)
│   ├── account-factory/          # deterministic deploy + discovery event
│   └── deployments.json          # testnet addresses + proof tx hashes
├── apps/
│   ├── matrix/                   # living compatibility-matrix data pipeline
│   ├── demo/                     # reference demo + /embed + on-chain testnet page
│   ├── landing/                  # soropass.dev
│   └── site/                     # docs front-end
└── docs/                         # security · integration · rfp · ui · matrix
```

---

