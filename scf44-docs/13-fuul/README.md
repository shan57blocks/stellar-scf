# Fuul

Fuul is a service that lets crypto companies reward their users. Examples: tokens for trading, for adding money to a pool, for holding a token, or for referring friends. A team picks the actions to reward and sets the rules. Fuul tracks who did what and pays out. Today it runs on Ethereum-style chains and Solana, for clients such as Coinbase, Kraken and dYdX. The SCF #44 grant adds Stellar, so Stellar projects can run the same reward programs.

## What SCF #44 pays them to build

Total award: $105,000.

- **Tranche 1 — Stellar in the Fuul platform ($35,000).**
  - The REST API accepts Stellar addresses and tokens ($13,000). A REST API is Fuul's web interface for other programs.
  - Stellar can be picked in the program setup screen ($11,000).
  - The JavaScript SDK (Fuul's code library for developers) can sign Stellar claim transactions ($8,000).
  - Stellar numbers in the analytics dashboard ($3,000).
- **Tranche 2 — "Trigger connectors" ($20,000).** Each connector watches one kind of on-chain activity so it can be rewarded.
  - Aquarius, a Stellar exchange: liquidity and trading volume ($8,000).
  - Blend v2, a Stellar lending app: lending and borrowing positions ($8,000).
  - Holders of any Stellar token ($4,000).
- **Tranche 3 — Soroban contracts and mainnet ($50,000).** Soroban is Stellar's smart contract system.
  - A reward distribution contract on testnet and mainnet ($45,000). It holds the budget and pays out claims, and Fuul never holds the funds.
  - The contract code published under the MIT license ($0).
  - A live claim page on mainnet that uses Stellar Wallets Kit, a library that connects many Stellar wallets ($3,000).
  - Public developer docs for Stellar ($2,000).

## How it works

The architecture page describes three layers:

1. **Platform.** Stellar becomes one more supported chain in Fuul's existing API, SDK, setup screens and analytics.
2. **Connectors.** Each connector is a separate indexer, a program that reads the blockchain and stores what it finds. It reads one app's activity from the Stellar ledger and turns it into Fuul's common event format.
   - Aquarius: Soroban pool events.
   - Blend: supply and borrow balances in each pool.
   - Token holders: balances of any token.

   Fuul's reward engine is the same on every chain. A new app only needs a new connector.
3. **Soroban reward contract.** One contract per project, and the project is its admin.
   - Fuul's engine issues signed "claim checks". Each says who gets what token, how much, why, and by when.
   - Users send their checks to the contract through Stellar Wallets Kit. The project can also send them on the user's behalf, so the user pays no fee.
   - The contract checks the signatures against a set of approved signers. It needs a set minimum number of them. It then pays out, and a check cannot be used twice.
   - Functions: `deposit(token, amount)`, `claim(claim_checks)`, and `withdraw` (admin only, to take back unspent budget).
   - Rewards can be XLM, stablecoins, or any SEP-41 token. SEP-41 is Stellar's standard token interface.

The page says the event indexer will be extended with Horizon's event stream (Horizon is Stellar's data API). Fuul's public SDK (`@fuul/sdk`) already has Stellar address types (`stellar_address`, `stellar_contract`). Its developer notes say Stellar signatures use the chain's own ed25519 scheme.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recTKSLmfcHgAn3X2 | submission.md | Awarded $105K |
| Stellar Technical Architecture (Notion) | architecture | https://app.notion.com/p/fuul-app/Stellar-Technical-Architecture-36ea045a4b2880769909d0e54fe99149 | architecture.md | Public. The "Data Flow" diagram is an image and was not copied |
| SCF #42 submission (earlier round) | earlier SCF submission | https://communityfund.stellar.org/submissions/reczUrUF68Gmj3QYe | scf42-submission.md | Same plan for $150K (tranches $30K / $45K / $60K). Not awarded |
| SCF #44 pitch video | demo | https://www.youtube.com/watch?v=AI0EDdDIOGg | — | "Unlocking DeFi Incentives on Stellar by Fuul" |
| SCF #42 pitch video | demo | https://youtu.be/Tzas5VtwAz4 | — | Same title, earlier round |
| Fuul SDK repo | code / docs | https://github.com/kuyen-labs/fuul-sdk | — | Published on npm as `@fuul/sdk` (latest 7.47.0). Has early Stellar address types |
| fuul-protocol GitHub org | code | https://github.com/fuul-protocol | — | Contracts for other chains only (EVM, Solana). No Soroban code yet |
| Developer docs | docs site | https://docs.fuul.xyz | — | No Stellar pages found in its sitemap |
| Case studies | evidence | https://www.fuul.xyz/case-studies | — | Linked in the submission |
| Fuul app | product | https://app.fuul.xyz | — | Linked in the submission |
| Website | website | https://www.fuul.xyz/ | — | |

## Gaps

- The Soroban reward contract is not public yet. It is promised as open source (MIT) in Tranche 3.
- The docs site has no Stellar docs yet. Stellar docs are a Tranche 3 deliverable.
- The architecture page's data flow diagram is an image, so it is not in `architecture.md`.
- The npm package page returns HTTP 403 to scripts. The npm registry shows the package is published.
- No pitch deck, whitepaper, or audit is linked.
