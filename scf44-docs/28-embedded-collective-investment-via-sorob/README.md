# Embedded Collective Investment via Soroban (Escala HQ)

Escala HQ builds a service that other companies plug into their own apps. It lets NGOs, cooperatives and remittance apps run community investment funds. People send small contributions, often a slice of each remittance (money sent home from abroad), into a shared fund. The members vote on local projects, and money is paid out step by step as each project shows progress. The go-to-market pilot is with UNDP (the United Nations Development Programme) in Guatemala. It grows out of the team's consumer app Amero. SCF #44 award: $70.0K (Integration Track).

## What SCF #44 pays them to build

- **Tranche 0 (upfront):** $7,000 (10%) on approval.
- **Tranche 1, MVP ($14,000).** Soroban contracts for the treasury (a USDC escrow with milestone logic) and for governance using Soulbound Tokens (SBTs, tokens that cannot be transferred, so each person has exactly one vote) ($5,000). Firebase login with OIDC (a standard login protocol) for multiple client organisations ($4,000). A first version of the "Cloud Studio" dashboard ($5,000). Proof: a public repo with the Rust contracts, testnet contract links on Stellar Expert, and a login demo video.
- **Tranche 2, Testnet ($21,000).** MoneyGram Access API as the on-ramp from cash to USDC ($8,000). The Stellar Disbursement Platform (SDP, Stellar's tool for bulk payouts) for milestone payouts ($7,000). Middleware to connect it all, plus tests ($6,000). Proof: Swagger/OpenAPI docs, and a testnet video of deposit → escrow → SDP payout.
- **Tranche 3, Mainnet ($28,000).** Mainnet deployment ($5,000), end-to-end QA and load testing ($8,000), developer docs and Cloud Studio polish ($7,000), and UNDP pilot onboarding ($8,000). Proof: mainnet contract addresses, live API endpoints, and a final video.

## How it works

- **B2B middleware:** a REST API (web interface for other apps) with multi-tenant headers (`X-Tenant-ID`, one per client organisation), JWT login and idempotency keys (so a repeated request does not pay twice).
- **Contributions:** a user of a client app (for example Amero) says yes to "contribute to a community fund". The API converts cash to USDC through MoneyGram and Circle. It then deposits the USDC into the TreasuryContract and mints Project Tokens that match the amount.
- **Soroban contracts:**
  - TreasuryContract: holds the pooled USDC. Its functions are `deposit`, `requestDisbursement` and `executeDisbursement`.
  - VotingContract: runs proposals, votes, vote delegation and counting (`createProposal`, `castVote`, `delegateVote`, `tally`).
  - Two tokens: a Governance Token (an SBT, one per member) and a Project Token (a normal token; voting power grows with the USDC contributed).
- **Milestone release:** the fund admin uploads evidence. An oracle (a trusted checker, for example a UNDP auditor) signs an attestation (a signed approval). The vote must pass. Then the treasury releases the tranche, and the middleware asks SDP to pay local vendors.
- **Cloud Studio:** a developer tool that uses MCP (Model Context Protocol, a way to connect AI assistants to tools). It helps client engineers write the setup code for Soroban, SDP and MoneyGram.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recTLN53hfYLoLT78 | submission.md | Tranches and budget |
| Architecture Design Document (v1.1, 13 Jun 2026) | architecture | https://docs.google.com/document/d/1w6gW5SwUEl7QZylIBi1bp34vaEAPZmL1/edit?usp=sharing&ouid=109175901284957979851&rtpof=true&sd=true | architecture.txt, architecture.docx | Flows, contracts, API endpoints, security. The diagrams (component, ER data model, state machine, flows) are images and are only in the .docx |
| OpenAPI (Swagger) spec | spec | https://github.com/escala-dev/collective-investment-api/blob/HEAD/swagger.yaml | api-spec-openapi.md | 22 endpoint paths, including MoneyGram and SDP webhooks |
| Live Swagger docs | spec | https://collectiveinvestment.api.escalahq.com/api-docs/ | — | Opens (HTTP 200). Linked from the architecture doc |
| API repo | code | https://github.com/escala-dev/collective-investment-api | — | Express API outline (a "mock architecture" per the doc). No README and no contract code yet |
| Demo video "SCF44 - Embedded Collective Investment via Soroban" | demo | https://youtu.be/0MvRisdFAjA | — | YouTube |
| Earlier submission, SCF #43 "Embedded Collective Investment Platform" ($70K, Prescreen Failed) | earlier SCF submission | https://communityfund.stellar.org/submissions/recdYL3NzcAk4co1G | scf43-submission.md | Almost the same plan and budget split as SCF #44 |
| Earlier submission, SCF #42 "Embedded Collective Investment" ($145K, Not Awarded) | earlier SCF submission | https://communityfund.stellar.org/submissions/recJUIsjYdCD9aWF6 | scf42-submission.md | Larger first version of the plan |
| UNDP blog: "Digital payments that work under real constraints" (19 Feb 2026) | blog | https://innovation.eurasia.undp.org/digital-payments-that-work-under-real-constraints/ | undp-blog-guatemala.md | Guatemala section only: the Amero Exchange remittance plus community fund pilot |
| SCF 2025 Impact Report (Medium) | blog | https://medium.com/stellar-community/stellar-community-fund-2025-impact-report-6f6c6361aaca | — | Medium blocks direct download (403). Read through a reader proxy: one sentence on the Amero and UNDP Guatemala pilot |
| Product website | website | https://collectiveinvestment.escalahq.com | — | Redirects to https://escalahq.com/collective. Built in the browser, so only the tagline can be read without a browser: "Turn a shared savings goal into a product people can follow." |
| Amero (the team's consumer app) | website | https://amero.lat | — | Opens |
| Fintech Americas awards | evidence | https://www.fintechamericas.co/ | — | Opens |

## Gaps

- No smart contract code is public yet. The only repo is an Express API outline with a Swagger file.
- The architecture diagrams are images inside the .docx. They are not in the text copy.
- The award evidence link https://x.fintechamericas.co/es/ganadores-2023-proyecto-amero-exchange returns 404.
- Medium returns 403 to scripts. Its content was checked only through a reader proxy.
