# Ballast Re

Ballast Re builds **baUSD**, a token on Stellar that earns yield from reinsurance. Reinsurance is insurance for insurance companies: they pay premiums to a reinsurer to share their risk. Investors deposit USDC (a dollar stablecoin) and get baUSD back. The money is used to back US casualty reinsurance contracts through a Bermuda reinsurer that Ballast is setting up. As premiums are earned, each baUSD is worth more. It is for KYC-verified institutional investors (KYC = identity checks), and later for wallets that offer it through DeFindex. The target is 6%+ a year. baUSD is not a stablecoin: its value can also go down if insurance losses come in.

## What SCF #44 pays them to build

Total award: $85.0K.

- **Tranche 1 - MVP ($17,000):** repo, CI and test setup ($3,000); Soroban vault contract for deposit, mint, redeem and share accounting ($8,000); baUSD token whose mint/burn is controlled only by the vault ($3,000); tests and testnet deployment ($3,000). Done when a deposit, mint, redeem cycle is demoed on testnet and a coverage report is public.
- **Tranche 2 - testnet, feature complete ($25,500):** on-chain NAV updates from off-chain attested premium data ($9,000) (NAV = net asset value, what the vault is worth); allowlist so only KYC'd investors can subscribe/redeem ($6,000); Reflector price feed and DeFindex strategy adapter ($6,500); React deposit/redeem app with Freighter wallet ($4,000).
- **Tranche 3 - mainnet ($34,000):** mainnet deployment and contract verification ($7,000); open-source release ($4,000); public NAV API, dashboard and monitoring ($13,000); developer docs and integration guide ($6,000); first real USDC deposit ($4,000).

## How it works

- **baUSD token.** A SEP-41 token (Stellar's standard token interface). Only the vault can mint or burn it.
- **Vault contract (Soroban).** Works like ERC-4626 (a standard "shares of a pool" vault): share price = total assets / total shares. Main calls: `subscribe`, `request_redemption`, `claim_redemption`, `update_nav`, `fund_sleeve`, `deploy_capital`, plus pause and upgrade.
- **Most money leaves the chain.** Only a small "liquidity sleeve" of USDC stays in the vault for withdrawals. The rest funds treaties via a Bermuda Class 3A segregated accounts company, collateralized in Reg 114 trusts. So `total_assets` is an attested number, not the vault's USDC balance.
- **NAV attestation.** An m-of-n multisig (several signers must agree) posts NAV updates. Updates are capped in size and frequency, carry a proof reference, and can go down.
- **Withdrawals** need a notice period. If the sleeve is short, the claim is partly paid and the rest waits.
- **Compliance.** KYC is done off-chain; approved addresses go on an on-chain allowlist.
- **Separate roles.** Governance, guardian (pause), attestation, compliance and treasury each have their own key.
- **Ecosystem links.** A SEP-40 price feed (Stellar's oracle interface; Reflector named in the plan) publishes the share price. A DeFindex strategy adapter lets DeFindex vaults allocate into baUSD. Wallet connection via Stellar Wallets Kit / Freighter. Blend, Soroswap, Aquarius and CCTP are roadmap only.
- **Current state (repo README):** contracts for token, vault, oracle and DeFindex strategy are deployed on testnet, unaudited. A web console is hosted.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission "baUSD: Onchain Reinsurance RWA Vault" | requirements | https://communityfund.stellar.org/submissions/reccpljqDQH7Miz7r | submission.md | Awarded $85.0K |
| baUSD Technical Architecture (June 2026) | architecture | https://drive.google.com/file/d/1tLV5Zi_L2jGzbPvEhCW985HHOnkYPmoH/view?usp=sharing | architecture.pdf, architecture.txt | Full spec: contracts, value flow, roles, risk, deployment plan |
| GitHub repo README | spec | https://github.com/chrisc999/ballast-re | repo-readme.md | Implemented functions, testnet contract IDs, demos. Repo not linked from the submission; owner matched by name only |
| Web console app README | docs site | https://github.com/chrisc999/ballast-re/blob/main/app/README.md | — | Short run instructions |
| Hosted testnet web console | demo | https://chrisc999.github.io/ballast-re/app/ | — | Needs Freighter on testnet |
| Test coverage report | demo | https://chrisc999.github.io/ballast-re/ | — | Tranche 1 success criterion |
| Demo recordings (GIFs) | demo | https://github.com/chrisc999/ballast-re/tree/main/media | — | Deposit/mint/redeem, NAV + compliance, DeFindex |
| SCF project page | earlier SCF submission | https://communityfund.stellar.org/project/recrf7E3a7KvlFQkR | — | Only the SCF #44 submission is listed; no earlier rounds |
| Website | docs site | https://www.ballastre.xyz | — | "Launching Soon" placeholder with a contact form only |

## Gaps

- No earlier SCF submissions exist for this project.
- The website has no content yet; no docs site, pitch deck, whitepaper or audit was found.
- The contracts are unaudited (the architecture plans an SCF Audit Bank audit after testnet).
- The GitHub repo is not linked from the submission; the link to the team is by name only (CEO Chris Comrie, GitHub user chrisc999).
