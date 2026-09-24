# SCF #44: POC Candidate Projects

As of 2026-09-24.

Ballast Re, SAFU Protocol and Fuul are the best SCF #44 test cases for an agent that turns a team's raw requirements into a deployed proof of concept (POC). Each one meets all three criteria:

1. **Clear, sufficient requirements.** The proposal or design doc names the contract functions, rules and "done when" checks.
2. **Simple scope.** One or two Soroban contracts (Soroban is Stellar's smart-contract platform), using standard Stellar tokens.
3. **A smart contract at the core.** The POC is mainly the contract, plus a small page or script to use it.

Of the 32 SCF #44 projects with public code, 12 were dropped for having no contract of their own, and 11 were dropped as too complex. The remaining 9 were ranked on how exact their requirements are.

## At a glance

| Project | What the POC is | Contracts | SCF #44 award | Stand-ins needed | Reference code |
| --- | --- | --- | --- | --- | --- |
| [Ballast Re](https://communityfund.stellar.org/submissions/reccpljqDQH7Miz7r) | Deposit USDC, get a share token, redeem after a notice period | 2 (vault + token) | $85.0K | Admin key posts the vault's value | [chrisc999/ballast-re](https://github.com/chrisc999/ballast-re) (~89 KB Rust) |
| [SAFU Protocol](https://communityfund.stellar.org/submissions/recn7V3oVzIqoijf2) | Shared pool that pays back members whose wallet was drained | 1 | $30.0K | Test key signs the fraud verdict | [mrkanchwala/safu-soroban](https://github.com/mrkanchwala/safu-soroban) (~199 KB Rust) |
| [Fuul](https://communityfund.stellar.org/submissions/recTKSLmfcHgAn3X2) | Reward budget paid out against signed claim checks | 1 | $105.0K (contract is one part) | Script signs the claim checks | None on Stellar yet |

## 1. Ballast Re: baUSD vault

The best fit: the design doc lists every contract function, and the first milestone has a single pass/fail test.

**What it is.** Investors deposit USDC (a digital dollar) and receive baUSD, a token whose value grows from reinsurance premiums. The vault works like a standard "shares of a pool" vault: share price = total assets ÷ total shares.

**Contracts**

- **baUSD token**: a standard Stellar token (SEP-41). Only the vault can mint or burn it.
- **Vault**:
  - `subscribe(amount)`: checks the allowlist, takes USDC and mints baUSD at the current share price.
  - `request_redemption(shares)`: starts a notice period.
  - `claim_redemption(request_id)`: after the notice period ends, burns baUSD and pays USDC.
  - `update_nav(new_total_assets, proof_ref)`: posts the vault's value (NAV, net asset value).
  - Also `fund_sleeve`, `deploy_capital`, pause and upgrade.

**Requirements (first milestone, $17,000)**

- Vault contract: deposit, mint, redeem, share accounting.
- baUSD minting and burning controlled only by the vault.
- Unit and integration tests, deployed to the test network.
- Done when: a deposit → mint → redeem cycle works on testnet, with a public test-coverage report.

**Stand-ins for the POC.** In real life most of the money leaves the chain to back reinsurance deals. For the POC, an admin key calls `update_nav`, and a simple allowlist replaces identity checks.

**Sources.** [Submission](https://communityfund.stellar.org/submissions/reccpljqDQH7Miz7r) · [Design doc](https://drive.google.com/file/d/1tLV5Zi_L2jGzbPvEhCW985HHOnkYPmoH/view?usp=sharing) · [Team's contract code](https://github.com/chrisc999/ballast-re) · Local copy: [scf44-docs/22-ballast-re](scf44-docs/22-ballast-re/README.md)

## 2. SAFU Protocol: wallet protection pool

One contract with exact numeric rules, which makes it easy to write pass/fail tests.

**What it is.** Members deposit XLM into a shared pool. If a member's wallet is drained (phishing, stolen key), an off-chain fraud checker confirms it, and the pool pays the member back over time. It ports the team's live Ethereum version (SAFUPool v6) to Stellar.

**Contract rules (ProtectionPool)**

- Tiered deposits (A / B / C) and points that grow with deposit time. Points unlock the right to claim (about 90 days).
- Claims must be filed within 30 days of the hack, with no backdated or future timestamps.
- Payout streams over 45 days, after a 7-day wait, at most 2% of the pool per day.
- Solvency rule: total promised payouts must never exceed total deposits.
- The contract trusts only a fraud verdict signed with the oracle's Ed25519 key.

**Requirements (first milestone, $6,000)**

- The ProtectionPool contract with all the rules above, compiled and deployed to testnet.
- At least 100 passing tests, a coverage report, and a storage-cost model.

**Stand-ins for the POC.** The real fraud checker is closed-source. For the POC, a test key signs the verdicts.

**Watch-out.** Exact point rates and tier ratios are only in the Ethereum version. Its code is public, so give it to the agent as extra input.

**Sources.** [Submission](https://communityfund.stellar.org/submissions/recn7V3oVzIqoijf2) · [Design doc](https://docs.google.com/document/d/1VZytHxGZzEmJE-BsBjygN3iuEVSYbSI9345JUcKGFFo/edit?tab=t.0) · [Stellar contract code](https://github.com/mrkanchwala/safu-soroban) · [Ethereum v6 code](https://github.com/mrkanchwala/safu-protocol) · Local copy: [scf44-docs/32-safu-protocol](scf44-docs/32-safu-protocol/README.md)

## 3. Fuul: reward claim contract

The smallest contract of the three, and the only one with no Stellar reference code, so it tests generation from requirements alone.

**What it is.** Fuul lets crypto projects reward users for trading, lending or referrals. The Stellar contract holds a project's reward budget and pays out "claim checks" signed by Fuul's reward engine. Fuul never holds the funds.

**Contract functions**

- `deposit(token, amount)`: the project funds the reward budget.
- `claim(claim_checks)`: pays one or more signed claim checks. Each check names recipient, token, amount, reason and deadline.
- `withdraw`: admin only, takes back unspent or expired budget.
- Each check needs signatures from a set of approved signers, meeting a minimum count. A check can be used only once.
- Rewards can be XLM, stablecoins or any SEP-41 token. One contract per project; the project is its admin.

**Requirements (third milestone, $50,000 of the $105,000 award; the contract itself is $45,000)**

- The reward contract on testnet and mainnet, published under the MIT license.
- A live claim page using Stellar Wallets Kit (a library that connects many Stellar wallets).
- Public developer docs for Stellar.

**Stand-ins for the POC.** A small script plays Fuul's reward engine and signs claim checks.

**Watch-out.** Most of Fuul's grant is adding Stellar to its existing platform. Use only the contract and claim page as the POC.

**Sources.** [Submission](https://communityfund.stellar.org/submissions/recTKSLmfcHgAn3X2) · [Architecture doc](https://app.notion.com/p/fuul-app/Stellar-Technical-Architecture-36ea045a4b2880769909d0e54fe99149?source=copy_link) · Local copy: [scf44-docs/13-fuul](scf44-docs/13-fuul/README.md)

## Runners-up and caveats

| Project | Why not in the top 3 | Use as |
| --- | --- | --- |
| Escala (collective investment) | 4 contracts plus signed approvals; clear function names and API spec | Medium-difficulty case |
| Tokeshare | 3 contracts (restricted token, fixed-price sale, revenue payout) | Medium-difficulty case |
| VRF-Soroban | One contract, but it must check advanced cryptographic proofs | Stress test |

These ratings come from the proposals, design docs and code sizes. No POC has been generated yet for any of these projects.
