# Lusty Finance

Lusty is an "options yield" app on Stellar. An option is a contract that pays someone for agreeing to sell (a "covered call") or buy (a "cash-secured put") an asset at a set price by a set date. On Lusty, a user deposits XLM or dollars as collateral, picks a price (the "strike") and a weekly Friday expiry, and gets paid a fee (the "premium") right away. It is for Stellar holders who want extra yield without running trading tools. It is live on Stellar testnet (the test network) at lusty.finance. The SCF #44 submission reports 211 wallets in the first month. The SCF project page shows $82.5K awarded and $24,750 paid so far.

## What SCF #44 pays them to build

Total $82.5K, in three tranches (payment stages).

- **Tranche 1, MVP: $16,500 (20%)**
  - On-chain custody: collateral moves out of a server-run account into the Soroban vault contract (a Soroban contract is a Stellar smart contract). Premium is paid in the same transaction as the deposit. Calls and puts each get their own collateral rules. ($7,000)
  - Signed quotes and settlement anyone can run: the contract only accepts prices signed by approved "quoters". Quoter changes need multisig (several keys must sign). A scheduled job calls `settle()` on expired positions, using the Reflector oracle price (an oracle is an on-chain price feed). ($5,500)
  - Risk dashboard and docs: portfolio view with delta and vega (measures of option risk), matched to the pricing engine, plus full protocol docs. ($4,000)
- **Tranche 2, Testnet: $24,750 (30%)**
  - BTC options, using wrapped BTC from a Stellar anchor (a regulated on/off-ramp company) and the Reflector BTC/USD feed, kept separate from XLM. ($8,250)
  - Multi-asset framework (a new asset is a config entry) and routing of LUSD/USDC to Stellar DEX and AMM liquidity, with slippage limits. ($8,250)
  - Public attack program: at least 14 days of open testing on testnet, a published report, and no open Critical/High findings. ($8,250)
- **Tranche 3, Mainnet: $41,250 (50%)**
  - Formal security review of the release candidate. ($10,000)
  - Mainnet deployment for XLM and BTC, settled in Circle's native USDC, with multisig admin accounts. ($18,000)
  - Controlled public launch with monitoring, alerting and incident procedures. ($13,250)

## How it works

- **Pricing engine (server).** One piece of code prices every option. It estimates XLM volatility from Binance price history, uses a standard options formula (Black-76), then takes a protocol cut. The screen shows exactly what the vault pays.
- **Two "rails" (ways money moves).**
  - Classic-escrow rail: this is what the live app uses. Collateral goes to a server-run "distributor" account, and the server pays the premium back.
  - Soroban rail: this is the trustless target. The vault contract holds the collateral. The writer and the quoter both sign one `deposit()` transaction, and the premium is paid inside that same transaction. At expiry, anyone can call `settle()`, which reads the Reflector oracle price at the expiry time. Grant work moves the app onto this rail.
- **Backend.** A Next.js web app with API routes, a Postgres (Supabase) database, a circuit breaker that can halt deposits, and scheduled jobs.
- **Stellar features used.** Soroban contracts, Stellar Asset Contracts (XLM, LUSD), the Reflector oracle, classic path payments for the XLM/LUSD swap, and account multisig. LUSD is the protocol's own testnet dollar.
- **Status in the repo (Sept 2026).** The repo docs record testnet runs of permissionless settlement, quoter rotation and a 2-of-3 admin multisig. They also record a separate BTC vault built from the same contract code (see `repo-testnet-evidence.md`).

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/rec3f7RaBpmtsN85N | submission.md | Awarded $82.5K |
| Technical Architecture Document (SCF #44) | architecture | https://docs.google.com/document/d/1reBUmKFLi-e2t2wrnDshMFH67w4G24sp/ | architecture.txt | Plain-text export; figures not included |
| Repo README | docs site | https://github.com/utkurock/Lusty | repo-readme.md | Status, how a position works, testnet addresses |
| Repo architecture doc | architecture | https://github.com/utkurock/Lusty/blob/main/docs/ARCHITECTURE.md | repo-architecture.md | Pricing, quote path, contract, caps, oracle, settlement |
| Soroban contracts README | spec | https://github.com/utkurock/Lusty/blob/main/contracts/README.md | contracts-readme.md | Vault v4 design, roles, testnet deployments |
| Key custody and security | spec | https://github.com/utkurock/Lusty/blob/main/docs/SECURITY.md | repo-security.md | Keys, multisig policy, what is still custodial |
| Testnet evidence, BTC book | demo | https://github.com/utkurock/Lusty/blob/main/docs/TESTNET-EVIDENCE.md | repo-testnet-evidence.md | Transaction hashes for BTC call and put runs |
| SCF #43 submission (earlier round) | earlier SCF submission | https://communityfund.stellar.org/submissions/rec9iLMyTbfXLCSlR | scf43-submission.md | Status: Panel Review Failed; earlier roadmap ($16.5K / $24.75K / $33K) |
| SCF #43 architecture doc (v0.1, April 2026) | architecture | https://drive.google.com/file/d/1vbqtb_sGVaPxFyLCZeIBrsdLKcvUjTK4/view?usp=sharing | scf43-architecture.md | Text pulled from the PDF; describes the earlier design with no smart contracts |
| Lusty docs site | docs site | https://lusty.finance/docs | — | Opens (200). Only the first page is readable without a browser. |
| Website | docs site | https://lusty.finance/ | — | Scripts get an HTTP 307 redirect back to the same page; `/docs` on the same site opens |
| Demo video (SCF #44) | demo | https://youtu.be/mkML5AbZLdg | — | "Lusty finance demo" |
| Demo video (SCF #43) | demo | https://youtu.be/zKW65Y9YZho | — | "lusty finance stellar demo" |

## Gaps

- No pitch deck, whitepaper or audit is linked. The formal security review is a Tranche 3 deliverable.
- The architecture doc copy is text only. Its figures (layer diagram, lifecycle, pricing pipeline) are missing.
- Most pages of the docs site at lusty.finance/docs load only in a browser, so they were not saved.
- The website root could not be checked with scripts (it returns a 307 redirect back to itself).
