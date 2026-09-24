# ChimpDAO

ChimpDAO is building a physical card that works as a Stellar wallet. A small NFC chip in the card (NFC = the tap technology in bank cards and phones) creates its own secret key and never lets it out. To do anything sensitive, like send money, swap coins, or put money into a savings-style strategy, you tap the card on an iPhone and the chip signs the action. It is for everyday users who find browser wallets and seed phrases hard, and for Stellar projects that want to hand out branded cards already loaded with value. ChimpDAO never holds user funds. The team (Barth Houot and Pamphile Roy, via Consulting Manao) already runs the same chip-signing idea on Stellar mainnet for NFC-tagged shirts linked to NFTs (an NFT is a one-of-a-kind on-chain token).

## What SCF #44 pays them to build

Total award: $110.0K, in three tranches (a tranche is one paid stage of the grant). Tranche totals below are the sum of the per-deliverable budgets in the submission.

- **Tranche 1 - MVP: card-authorized wallet, integration-ready ($33K)**
  - Card authorization contracts on testnet: card registration, activation, replay protection, lock/revoke ($5K).
  - iOS app becomes a card wallet: activation, NFC signing, balance, transaction status ($5K).
  - Fund the card from other Stellar wallets (Freighter / Stellar Wallets Kit), plus a web page for partner onboarding docs ($5K).
  - Adapter layer so protocols (DeFindex, Soroswap first; Etherfuse, Near Intents second) plug into card signing ($4K).
  - Test that the chip works in a real card shape (antenna, thickness, material); make a sample ($3K).
  - Confirm integration scope with partners ($5K).
  - Implementation plan and the exact format of what the card signs ($6K).
- **Tranche 2 - Testnet: tap-to-swap, tap-to-yield, tap-to-fund ($33K)**
  - DeFindex (yield vaults): show strategies, deposit, track, withdraw by tapping ($13K).
  - Soroswap (token swaps): quotes, swap review, card-signed swap bound to the route ($8K).
  - Etherfuse: hold a yield-bearing stable asset and show its growth ($4K).
  - Near Intents: proof of concept for funding the card from another blockchain ($3K).
  - Full end-to-end testnet demo, including a partner-issued card / "pending-claim vault", and a reviewer guide ($5K).
- **Tranche 3 - Mainnet launch ($44K)**
  - Hardened contracts on mainnet, runbook, incident plan ($9K).
  - iOS mainnet release candidate on TestFlight ($9K).
  - DeFindex and Soroswap on mainnet; Etherfuse where available ($13K).
  - Internal security hardening and failure tests (not a paid audit) ($6K).
  - Partner-led test session, reviewer product page, user and integration guides, updated architecture doc ($7K).

## How it works

Source: the SCF #44 Technical Architecture Document V2 (`architecture.txt`) and the current contracts repo docs.

- **Four layers** (architecture doc):
  1. *Physical card*: secure NFC chip, key made on the chip, cannot be copied out, talks to the phone over APDU (the command format smart cards use).
  2. *iOS app*: activation, balances, transaction review, NFC signing prompts, talks to Stellar through Stellar RPC (the server apps use to read the chain and send transactions).
  3. *Stellar execution*: Soroban smart contracts (Stellar's programmable contracts). The preferred design is a "card-linked contract account": a contract wallet that holds the assets and only accepts actions signed by the registered card.
  4. *Ecosystem integrations*: DeFindex, Soroswap, Blend (lending) through DeFindex strategies, Stellar Wallets Kit-compatible wallets.
- **What the card signs**: never a blanket approval. It signs one exact action (for example TRANSFER_ASSET, EXECUTE_SOROSWAP, DEPOSIT_DEFINDEX) with the amount, asset, destination, target contract, route, nonce (a one-time number that stops replays) and expiry.
- **Planned contract modules** (architecture doc): CardRegistry, CardAccount / smart wallet, NonceManager, PolicyManager, DeFiRouter, RecoveryModule.
- **Transaction flow**: app builds the call, card signs it by tap, app simulates it on Stellar RPC, submits it, then polls for the result.
- **What the repo does today** (`repo-architecture.md`, `repo-auth.md`): the live code is simpler than the architecture doc. A "Chimp account" is a set of cards, any one of which can sign. Each card also has a "Pocket" purse contract. Both use Soroban's custom-account check, so Stellar itself binds the signature to the exact call and handles replay and expiry. The repo says there is no policy layer, no app-level nonce and no OpenZeppelin code. The NFT contracts (`nfc-nft`, `collection`) are on mainnet. Supported chips: Infineon SECORA and NXP MIFARE DUOX.
- **Custody**: ChimpDAO says it never holds funds or keys and does not promise yield.

## Documents

| Document | Type | Link | Local copy | Notes |
|---|---|---|---|---|
| SCF #44 submission "Tap-to-Use Stellar DeFi Card" | requirements | https://communityfund.stellar.org/submissions/recXeMtBHrhDbdtm7 | submission.md | Tranches, deliverables, budgets |
| Technical Architecture Document V2 | architecture | https://docs.google.com/document/d/1ED8c_HOQKdhy9oeB6HDQlywWLLw8nQR4CJVusCPMTfg/edit?usp=sharing | architecture.txt | Public. Re-checked: same as current export. Diagrams are images and are not in the text copy. Mentions Etherfuse only briefly and Near Intents not at all. |
| Contracts repo README + NFC chip guide | docs | https://github.com/Consulting-Manao/chimpdao-contracts | repo-readme.md | Includes README_NFC.md; mainnet contract IDs |
| Contracts architecture | architecture | https://github.com/Consulting-Manao/chimpdao-contracts/blob/main/docs/ARCHITECTURE.md | repo-architecture.md | Crate map, deploy and lifecycle diagrams (Mermaid) |
| Contracts auth design (ChipAuth) | spec | https://github.com/Consulting-Manao/chimpdao-contracts/blob/main/docs/AUTH.md | repo-auth.md | How chip signatures are checked; replay handling |
| iOS app repo | code | https://github.com/Consulting-Manao/chimpdao-ios | — | Swift app; no README or docs files in the repo |
| NFT viewer repo | code | https://github.com/Consulting-Manao/chimpdao-nft | — | README is one line |
| chimpdao-nfc-bridge (Radicle) | code | https://radicle.network/nodes/radicle.consulting-manao.com/rad%3Az2CDTfvUguLG3UboK46HyYxoxg1og | — | USB NFC reader bridge; hosted on Radicle, not GitHub |
| chimpdao-terminal (Radicle) | code | https://radicle.network/nodes/radicle.consulting-manao.com/rad%3Az4Y793TkQB4X4Uz4CRdEMUHxakZKt | — | Merchant tap-to-pay terminal |
| PaltaLabs letter of interest (Soroswap, DeFindex) | evidence | https://drive.google.com/file/d/1La3a3Fji123D81l3T_FTaDdB5gYS5CcB/view?usp=sharing | — | 1-page PDF, non-binding, dated 01/07/2026 |
| Etherfuse letter of interest | evidence | https://drive.google.com/file/d/1kHdUxKuAOsTtjThmxcuMOADd0BzbXRMn/view?usp=sharing | — | 1-page PDF, non-binding, dated 01/07/2026 |
| Pitch video (SCF #44) | demo | https://youtu.be/rXLyveRnJsU | — | "ChimpDAO Pitch - Integration Track" |
| Project pitch deck (Canva) | pitch | https://www.canva.com/design/DAGodFHF-Fc/xN2yzphsJJUG6na_8ziLQA/view | — | Listed as pitch deck on the SCF page. Canva blocks automated reading, so text was not extracted |
| Website | website | https://chimpdao.xyz/ | — | Product site; links shop, NFT viewer, iOS app. No docs linked |
| iOS app (App Store) | product | https://apps.apple.com/us/app/chimpdao/id6757618362 | — | Linked from the website |
| SCF project page | earlier SCF submission | https://communityfund.stellar.org/project/recQOmskc9uNB1kzH | — | Lists SCF #44 and SCF #38 |
| SCF #38 submission "Stellar Merch Shop" | earlier SCF submission | https://communityfund.stellar.org/submissions/rece7XluClIM1t7EL | scf38-submission.md | Awarded $105.0K. NFT-linked Stellar merch shop with DAO; the origin of the NFC-NFT work |
| SCF #38 infrastructure specification | architecture | https://docs.google.com/document/d/1y8tlgZi5UyObRoe0pXJfjQWrKpAOz19NLrQar7pi7sU/edit?usp=sharing | scf38-merch-shop-infra-spec.md | Earlier design for the merch shop contracts and dApp |
| SCF #38 deck (Canva, "Website" button) | pitch | https://www.canva.com/design/DAGvqViz_jY/SHMC1PklN_o6qUO-pjRpUQ/view | — | Canva blocks automated reading |
| SCF #38 video | demo | https://youtu.be/yhxx18oONqQ | — | "SCF38 Stellar Merch Shop proposal" |

## Gaps

- The submission lists `https://github.com/Consulting-Manao/chimpdao-contracts%EF%BF%BCiOS`. It returns 404. It is a copy-paste error that joins the contracts link and the word "iOS".
- The two Canva decks could not be read automatically (bot check). Open them in a browser to read them.
- The architecture doc's diagrams (system, account model, auth flow, contract modules) are images and are missing from `architecture.txt`.
- The architecture doc (V2) does not cover Near Intents and only briefly mentions Etherfuse, though both are funded deliverables. Deliverable 6 of Tranche 1 promises an updated doc.
- The contracts repo design (card set + Pocket, no policy module, no app nonce) differs from the modules planned in the architecture doc. No document explains the change.
- No SCF #44 testnet contract IDs, reviewer guide or card form-factor spec were found yet. Those are grant deliverables.
- The iOS repo has no README or docs.
