# SAFU Protocol

A shared "protection pool" for Stellar wallets. Members put XLM into a common pool. If a member's wallet is drained (by phishing, a stolen key, etc.), the pool pays them back over time. It is for everyday Stellar users who hold funds in wallets. It is a port of SAFUPool v6, which the same team (Murtaza Kanchwala and Deniz Köse) runs on Ethereum at safustaking.com.

## What SCF #44 pays them to build

Total award: $30,000 (Financial Protocols category).

- **Tranche 1: MVP ($6,000).** The ProtectionPool smart contract: tiered deposits, points, 30-day claim window, payout stream (max 2% per day), a rule that the pool never owes more than it holds. At least 100 tests and a storage-cost model. Deployed on testnet.
- **Tranche 2: Testnet ($9,000).** Fraud oracle adapted to Stellar, with the contract checking its signature. Yield: idle funds go through DeFindex into Blend. Stellar web app (Freighter / Stellar Wallets Kit): deposit, claim, status. Testnet end-to-end test and demo video.
- **Tranche 3: Mainnet ($12,000).** Mainnet launch and full deposit-to-payout test with demo video. Fix findings from the SCF Audit Bank audit (audit paid by SCF). User-testing fixes.

## How it works

- **ProtectionPool contract.** A Soroban smart contract (Soroban = Stellar's smart contract platform) in Rust. It tracks deposits, tiers, points and claims, and streams payouts over 45 days (7-day wait, max 2% of the pool per day).
- **Money movement.** Uses the Stellar Asset Contract (SAC, the standard token interface) for XLM transfers. Users sign with `require_auth`.
- **Fraud oracle (off-chain, closed-source).** A Python program that looks at a drain transaction and decides if it qualifies. It signs a verdict with an Ed25519 key. The contract only checks that signature. Sensitive actions need two signers (oracle + co-signer).
- **Yield.** Spare pool money goes into Blend (a Stellar lending protocol) through a DeFindex vault (a yield tool). The yield is protocol revenue. Payouts come only from deposited money, not yield.
- **Frontend.** The existing safustaking.com site, adapted for Stellar wallets.

Per the Soroban repo README, all three tranches are delivered and the contract is live on Stellar mainnet since 2026-09-10 (`CB3LZVWKGGWSYHHIE7ILK5CJH2MLUB6SWAU7UK6PMQEP3AESD3DAUBRC`). The SCF audit was still waiting for an auditor.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recn7V3oVzIqoijf2 | submission.md | Only SCF submission on the project page |
| Technical Architecture: Community Protection Pools on Soroban | architecture | https://docs.google.com/document/d/1VZytHxGZzEmJE-BsBjygN3iuEVSYbSI9345JUcKGFFo/edit?tab=t.0 | architecture.txt | |
| Soroban contract repo README | docs | https://github.com/mrkanchwala/safu-soroban/blob/main/README.md | soroban-repo-readme.md | Tranche status, testnet and mainnet addresses |
| Testing methodology | spec | https://github.com/mrkanchwala/safu-soroban/blob/main/TESTING.md | testing.md | Tests, mutation testing, fuzzing, security passes |
| Reviewer kits (verify a payout yourself) | docs | https://github.com/mrkanchwala/safu-soroban/tree/main/reviewer-kit-mainnet | — | Testnet kit in `reviewer-kit/` |
| Ethereum (v6) repo README | docs | https://github.com/mrkanchwala/safu-protocol/blob/main/README.md | evm-repo-readme.md | Earlier EVM version the port is based on |
| Oracle runbook (EVM repo) | docs | https://github.com/mrkanchwala/safu-protocol/blob/main/docs/oracle-runbook.md | — | Not copied |
| Website | docs site | https://safustaking.com/ | — | Live Ethereum app |
| Mainnet contract | demo | https://stellar.expert/explorer/public/contract/CB3LZVWKGGWSYHHIE7ILK5CJH2MLUB6SWAU7UK6PMQEP3AESD3DAUBRC | — | From repo README |
| Tranche 2 testnet contract | demo | https://stellar.expert/explorer/testnet/contract/CDTXVIA4TSQ6PY76VFD4BBW4R4UMGSE5HTBNAMASAPRYRNV37DBDJJBB | — | |
| Hashlock AI audit (Ethereum v6) | audit | https://aiaudit.hashlock.com/audit/890ab9ec-8311-423f-9bd1-7d4a3cce48f8 | — | Returned HTTP 404, could not open |

## Gaps

- The website https://safustaking.com did not respond on 2026-09-24 (timed out after 40 seconds).
- The Hashlock audit report link from the submission returns 404.
- The submission promises a testnet web app URL and demo videos; neither was found in the repos. The README instead offers "reviewer kits" to replay payouts.
- The fraud-scoring engine is closed-source, so its logic is not documented.
- The submission points to `safu-protocol` (the Ethereum repo) for source; the Soroban code is actually in `safu-soroban`.
- No SCF Audit Bank audit report yet (still waiting for an auditor per the README).
- No earlier SCF submissions: the project page lists only SCF #44.
