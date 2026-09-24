# VRF-Soroban (Soroban VRF)

A randomness service for apps on Stellar. A "VRF" (verifiable random function) gives a random number plus a proof that anyone can check, so nobody can secretly rig it. It is for developers of on-chain games, NFT mints and DAO votes who need fair random numbers. Apps pay a small fee per request. The team (Mostafa and Gemy Lotfy, GitHub `NibrasD`) had a testnet prototype before SCF #44.

## What SCF #44 pays them to build

Total award: $50,000 (Developer Tooling category).

- **Tranche 1: MVP ($10,000).** Production smart contract (request, fulfill, derive random number, timeout refund). Every request is tied to a future drand round. On-chain proof checks with Stellar's built-in BLS12-381 math. One off-chain oracle worker on testnet. Full request-to-result flow proven on testnet, under 70M instructions per transaction.
- **Tranche 2: Testnet ($15,000).** Callbacks: the contract hands the random number straight to the app's contract. Timeout/refund logic, storage-expiry edge cases, key rotation. Written threat model and failure tests. Performance tuning.
- **Tranche 3: Mainnet ($25,000).** Mainnet launch. Main oracle plus a hot standby with automatic failover. JavaScript and Rust SDKs (software kits). Example app, runbooks, a public dashboard and a "playground" page.

## How it works

- **VRF contract (on Stellar).** A Soroban smart contract (Soroban = Stellar's smart contract platform). It takes requests and fees, locks in which future drand round to use, checks the proof, stores the result, and calls back the app's contract.
- **drand.** A public randomness "beacon" (a network that publishes a new random value every 3 seconds, "quicknet" chain). Each request must use a round that does not exist yet (at least 2 rounds ahead). So nobody can know the input in advance.
- **Oracle worker (off-chain).** Watches for requests, waits for the drand round, signs it with its secret key (the BLS-VRF proof, `gamma = sk * H(alpha)`), and sends a `fulfill` transaction.
- **On-chain check.** The contract checks both the drand signature and the oracle proof with Stellar's native BLS12-381 functions (CAP-0059, CAP-0080). Cost is about 58M instructions.
- **Consumer app.** Calls `request()` and gets the random value by callback or via `derive_random_in_range()`.
- **Fees.** Paid through the Stellar Asset Contract (SAC, the standard token interface) and held until fulfilled or refunded.

Per the repo README, it is now live on Stellar mainnet (contract `CBTCC5QL5T3JSLEZO4PH6LSJYEQF6GEFDCAO67OXI4DTM5NXMK6TSUHU`), with npm and crates.io SDKs `stellar-vrf-sdk`.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recHHh2SgBoGOtcZ3 | submission.md | Only SCF submission on the project page |
| Technical Architecture Document | architecture | https://docs.google.com/document/d/1EwEmEiNicr-QzThme4nyjk3Dz2KMerTsEiPvny_7Hy0/edit | architecture.txt | Text export; the system diagram image is not included |
| Repo README | docs | https://github.com/NibrasD/Stellar-VRF/blob/main/README.md | repo-readme.md | Live mainnet addresses, SDK quick start, known limits |
| Threat model | spec | https://github.com/NibrasD/Stellar-VRF/blob/main/docs/THREAT_MODEL.md | threat-model.md | Trust assumptions and risks |
| Automated security review | audit | https://github.com/NibrasD/Stellar-VRF/blob/main/docs/AUDIT_REPORT.md | audit-report.md | Automated tool (Plamen), not a human firm; marked historical by the team |
| Operations guide | docs | https://github.com/NibrasD/Stellar-VRF/blob/main/docs/OPERATIONS.md | operations.md | Mainnet operations, storage TTL |
| Other repo docs (HA deployment, failover evidence, runbook, profiling, SDK release, STRIDE threat model, consumer authorization) | docs | https://github.com/NibrasD/Stellar-VRF/tree/main/docs | — | Not copied |
| Dashboard | demo | https://nibrasd.github.io/Stellar-VRF/dashboard/ | — | |
| Playground | demo | https://nibrasd.github.io/Stellar-VRF/playground/ | — | |
| Integration guide / example dApp | docs | https://nibrasd.github.io/Stellar-VRF/example-dapp/ | — | |
| Testnet MVP contract | demo | https://stellar.expert/explorer/testnet/contract/CAUL2Y45FMGRSELVIO2QVNNJ4GQ4SQADY2QYTZCRW7K6YINU5QWLD2UT | — | Traction evidence in the submission |
| Mainnet contract | demo | https://stellar.expert/explorer/public/contract/CBTCC5QL5T3JSLEZO4PH6LSJYEQF6GEFDCAO67OXI4DTM5NXMK6TSUHU | — | From repo README |
| JS SDK on npm | docs | https://www.npmjs.com/package/stellar-vrf-sdk | — | npm website blocks scripts (403); package exists in the npm registry |
| Rust SDK on crates.io | docs | https://crates.io/crates/stellar-vrf-sdk | — | Web page needs a browser (404 to scripts); crate exists in the crates.io API |
| Team's earlier repos (transaction visualizer, Hummingbot connector, concentrated liquidity) | other | https://github.com/NibrasD/stellar-transaction-visualizer, https://github.com/NibrasD/stellar-hummingbot-connector, https://github.com/NibrasD/Stellar-Concentrated-Liquidity | — | Team background, not this product |

## Gaps

- The frontend `https://soroban-vrf-frontend.onrender.com` returned HTTP 503 (down).
- No third-party (human) audit found. The only audit file is an automated review the team marks as outdated.
- The architecture diagram in the Google Doc is an image and is not in the text copy.
- No earlier SCF submissions: the project page lists only SCF #44.
