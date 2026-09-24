# JUMPA

JUMPA is a wallet you use by chatting. You type things like "Swap 50 XLM for USDC" or "Save $20 into my savings goal". An AI reads the message and turns it into a blockchain transaction. You confirm with a 6-digit PIN. It is for everyday users in emerging markets (Sub-Saharan Africa, Southeast Asia) who want to hold and spend stablecoins (crypto tokens pegged to the US dollar) without seed phrases or buying gas tokens. It already supports Solana and Base; SCF #44 pays to add Stellar. SCF #44 award: $89.7K.

## What SCF #44 pays them to build

- **Tranche 1 – MVP: core integration and SDK foundations ($30,000).** Stellar key derivation (path m/44'/148'/0') and Horizon account sync. Soroswap quote and transaction-build handlers. SEP-24 sandbox ramps (Mercuryo, MoneyGram) in in-app sheets. AI intent engine outputs Stellar swap, deposit and withdraw actions. Proof: video of a chat swap quote on testnet; SEP-24 on-ramp window opening in the app.
- **Tranche 2 – Testnet: conversational loop and DeFi yield ($26,700).** Full chat swaps on testnet (quote, build, PIN-sign, submit to Horizon). Savings goals wired to DeFindex testnet pools. Allbridge testnet bridging from Base to Stellar. Proof: video of a full swap with transaction hashes; logs of DeFindex deposits and withdrawals.
- **Tranche 3 – Mainnet: production launch ($33,000).** Soroswap, DeFindex, Allbridge and MoneyGram/Mercuryo live on mainnet. Public user and developer guides on the Jumpa website. Targets: 50+ mainnet transactions, $5,000+ swapped or bridged, 20+ active savings goals, public production dashboard.

## How it works

- **Frontend and API:** one Next.js app (a React web framework). UI parts live under `components/stellar`, logic under `lib/stellar`, server endpoints under `app/api/stellar`. Data is stored in MongoDB.
- **Chat loop:** user message → AI intent parser → confirmation card in chat → PIN screen → the browser decrypts the key and signs → signed transaction (XDR, Stellar's transaction format) sent to Horizon (Stellar's public API server).
- **Keys ("sovereign security"):** the seed phrase is encrypted in the browser with AES-256-GCM, using a key derived from the PIN (PBKDF2). The server only stores the encrypted blob, salt and IV.
- **Four Stellar integrations:**
  - **Soroswap** (a Stellar DEX aggregator): quotes and swap routing. The repo docs say swaps call the Soroswap router Soroban contract.
  - **SEP-24 anchors** (SEP-24 = Stellar standard for deposit/withdraw pages hosted by a fiat on/off-ramp company; SEP-10 = sign-in for anchors): MoneyGram and Mercuryo, shown in an iframe. A webhook tracks status.
  - **DeFindex** (Soroban yield vaults): "target savings" goals that deposit USDC and track APY.
  - **Allbridge Core** (cross-chain bridge): moves stablecoins from Solana/Base to Stellar.
- **Gas abstraction:** users should not need to hold XLM for fees (claimed in the submission).

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission | requirements | https://communityfund.stellar.org/submissions/recbAXB0DwzTWo81d | submission.md | Tranches, budgets, completion criteria |
| Technical architecture (PDF) | architecture | https://drive.google.com/file/d/1F8iuDz-M80NFkBKtXLyGx-VJcR3CNP7b/view | architecture.pdf, architecture.txt | Text extracted from the PDF |
| Technical architecture (Markdown source, with Mermaid diagrams) | architecture | https://github.com/official-jumpa/jumpa-website/blob/main/jumpa-stellar-architecture.md | — | Same content as the PDF |
| Architecture and integration document (SCF submission text) | spec | https://github.com/official-jumpa/jumpa-website/blob/main/community-fund-stellar.md | integration-doc.md | Product features and Stellar integration plan |
| Tranche 1 completion guide | spec | https://github.com/official-jumpa/jumpa-web-app/blob/main/docs/TRANCHE_1_COMPLETE.md | tranche-1-complete.md | Team's own claim of T1 delivery |
| Tranche 2 completion guide | spec | https://github.com/official-jumpa/jumpa-web-app/blob/main/docs/TRANCHE_2_COMPLETE.md | tranche-2-complete.md | Team's own claim of T2 delivery, with tx hashes |
| Testing guide | docs | https://github.com/official-jumpa/jumpa-web-app/blob/main/docs/HOW_TO_TEST.md | — | Step-by-step manual test walkthrough |
| jumpa-web-app repo | code | https://github.com/official-jumpa/jumpa-web-app | — | Main web app with Stellar module |
| jumpa repo (Telegram bot) | code | https://github.com/official-jumpa/jumpa | — | Has STELLAR_TRUSTLINES_AND_ACTIVATION.md |
| jumpa-website repo | code | https://github.com/official-jumpa/jumpa-website | — | Linked from the submission |
| Website | docs site | https://jumpa.xyz | — | Product site |
| Web app | demo | https://usejumpa.com | — | Live app (repo homepage) |
| Traction evidence | pitch | https://drive.google.com/file/d/1EOJVN664Lh60cX0vmNOQrNM6In5zOyOy/view?usp=drivesdk | — | A single photo of two people holding a signed paper; no numbers |
| SEVCP selection post | blog | https://x.com/a_nitapounds/status/2044750693408383043 | — | Traction claim |
| Superteam NG recognition post | blog | https://x.com/superteamng/status/2041136417678512519 | — | Traction claim |
| SCF #43 submission "Automated Payment Platform" | earlier SCF submission | https://communityfund.stellar.org/submissions/recNHNLYzQYP64pmt | scf43-submission.md | Not funded (prescreen failed). Broader scope: anchor, USSD, RWAs, virtual cards |
| SCF #43 architecture doc | earlier SCF submission | https://docs.google.com/document/d/1GCSqjQPSkfZWO5Te1NIDxaC1H7ntl2GhPM8j4U2IBQ8/edit | scf43-architecture.md | Older design built around SEP anchors, Soroban contracts and Switch ramps |
| SCF #43 pitch deck | pitch | https://drive.google.com/file/d/18weSIO7v84JRbInVDujnA35ApSSayhVJ/view?usp=drivesdk | — | Unreachable (404) |

## Gaps

- The SCF #43 pitch deck link returns 404. No pitch deck is linked for SCF #44.
- The "traction evidence" file is only a photo. It does not show the claimed 87 testers, 450+ transactions or $3,000 volume.
- No demo video is linked in the submission. The T1/T2 proof videos are not linked from the repo docs we read.
- The public user/developer guides promised for Tranche 3 were not found on jumpa.xyz yet.
- The tranche completion guides are written by the team. They have not been independently checked here.
