# Ballast Re: baUSD: Onchain Reinsurance RWA Vault

Source: https://communityfund.stellar.org/submissions/reccpljqDQH7Miz7r (SCF #44, awarded $85.0K, category: End-User Application)

- Website: https://www.ballastre.xyz
- Architecture doc: https://drive.google.com/file/d/1tLV5Zi_L2jGzbPvEhCW985HHOnkYPmoH/view?usp=sharing

## Links in the submission

- https://drive.google.com/file/d/1tLV5Zi_L2jGzbPvEhCW985HHOnkYPmoH/view?usp=sharing

## Submission text (as published)

```text
Products & Services
Ballast Re delivers baUSD, a yield-bearing token issued natively on Soroban and backed by regulated US casualty specialty reinsurance treaties. baUSD brings a new, uncorrelated real-world asset class to Stellar: institutional reinsurance yield, onchain for the first time.
The build comprises:
baUSD vault: a Soroban contract where users deposit USDC on Stellar and mint baUSD, an appreciating share token (ERC-4626-equivalent) whose value rises as earned reinsurance premium accrues. Target 6%+ APY, uncorrelated to crypto, equities, and rates because it derives from insurance underwriting profit, not emissions or duration. All share accounting, mint/burn, and NAV accrual execute in Soroban, giving Stellar users a yield source independent of every other onchain return.
Compliant onboarding: allowlist-gated subscription/redemption for KYC-verified institutional LPs, settling natively on Stellar, routing regulated institutional capital onto Stellar rails.
Ecosystem distribution: baUSD offered as a DeFindex strategy so any DeFindex-integrated Stellar wallet or app can surface reinsurance yield to users, with a Reflector price feed enabling composability, turning baUSD into wallet-distributable yield rather than idle TVL.
Underlying capital flows to our Bermuda Class 3A Segregated Accounts Company reinsurer; underwriting profit on bound treaties accrues to baUSD. Principal sits behind a collateral waterfall: earned premium reserves, Reg 114 Trust loss reserves, segregated-account protection, and retrocessional tail cover.
Requested Budget
$85.0K
Traction Evidence
Capital: ~$30M in LP commitments
Origination: quota share US casualty specialty treaty in the bind pipeline
Regulatory: Ballast Re SAC Ltd. formation in progress (BMA Class 3A track); BVI management entity Ballast Management Ltd.
Insurance Underwriting expertise: CUO Daniel Heinlein underwrote $3B+ in premium as former CEO of JRG Re; independent actuarial review of underwriting models 
Ecosystem: guided through Stellar by Punia and Justin Shaw (SDF)
Tranche 1 (Deliverable Roadmap) - MVP
Tranche 1 — MVP (20% · $17,000)
Deliverables:
Repository, CI/CD, development environment, and test-harness scaffolding $3,000
Soroban vault contract: deposit, mint, redeem, share-accounting logic (appreciating-share / ERC-4626-equivalent) $8,000
baUSD issued as a Stellar asset with mint/burn authority bound to the vault contract (no discretionary issuance) $3,000
Unit + integration test suite and testnet deployment $3,000
Definition of success: deposit → mint → redeem cycle recorded as a demo on Stellar testnet; public test-coverage report published.
Total Budget: $17,000
Tranche 2 (Deliverable Roadmap) - Testnet
Tranche 2 — Testnet / feature complete (30% · $25,500)
Deliverables:
NAV / yield-accrual: authorized onchain NAV updates driven by off-chain attested earned-premium data $9,000
Allowlist compliance gating on subscribe/redeem for KYC-verified institutional LPs $6,000
Reflector price feed for baUSD + DeFindex strategy adapter exposing baUSD as a selectable strategy $6,500
React deposit/redeem dApp with Freighter wallet integration $4,000
Definition of success: oracle-priced NAV live on testnet; allowlisted subscribe/redeem demo recorded; baUSD selectable as a DeFindex strategy on testnet.
Total Budget: $25,500
Tranche 3 (Deliverable Roadmap) - Mainnet
Tranche 3 — Mainnet launch (40% · $34,000)
Deliverables:
Mainnet deployment and contract verification $7,000
Open-source release: tagged release, license, README with deployment addresses $4,000
Public NAV API + dashboard + monitoring/alerting stack $13,000
Developer documentation and integration guide $6,000
First real USDC deposit milestone and launch validation $4,000
Definition of success: vault live on Stellar mainnet with first real USDC deposit; baUSD live as a DeFindex strategy on mainnet; public NAV dashboard live; contracts open-sourced.
Total Budget: $34,000
Team
Four co-founders, 30+ combined years across specialty reinsurance, structured finance, and DeFi infrastructure.
Chris Comrie, CEO: 10 years across insurance and DeFi. Former RT Specialty, Markel, and AmWINS in E&S casualty wholesale brokerage. CMO of De.Fi application that raised $5M from Shima Capital and Consensys. LinkedIn:
https://www.linkedin.com/in/comriechris/
Daniel Heinlein, CUO (Chief Underwriting Officer): 20 years in reinsurance. Former CEO of JRG Re and senior executive at Willis Re. $3B+ premium underwritten across casualty and specialty lines with consistently profitable loss ratios. LinkedIn:
https://www.linkedin.com/in/daniel-heinlein-ba974a48/
Tomi Fyrqvist, CFO: Former Goldman Sachs and AXA Ventures. CFO of Phaver ($7M raised from Nomad Capital and Polygon Ventures). $50M+ in venture raises led. LinkedIn:
https://www.linkedin.com/in/tomi-fyrqvist/
Tommy Le, CTO: 20+ years in enterprise software and Web3. Co-founder of SotaTek; former Business Unit Director at FPT Software. Also founder of Web3 ventures Ekotek, Monsterra, and ERAGON. LinkedIn:
https://vn.linkedin.com/in/tuanle0804
Chris Comrie
```
