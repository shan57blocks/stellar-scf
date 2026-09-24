# SAFU Protocol: Stellar Wallet Protection Pools

Source: https://communityfund.stellar.org/submissions/recn7V3oVzIqoijf2 (SCF #44, awarded $30.0K, category: Financial Protocols)

- Website: https://safustaking.com/
- Architecture doc: https://docs.google.com/document/d/1VZytHxGZzEmJE-BsBjygN3iuEVSYbSI9345JUcKGFFo/edit?tab=t.0

## Links in the submission

- https://docs.google.com/document/d/1VZytHxGZzEmJE-BsBjygN3iuEVSYbSI9345JUcKGFFo/edit?tab=t.0
- https://github.com/mrkanchwala/safu-protocol
- http://github.com/mrkanchwala/safu-protocol

## Submission text (as published)

```text
Products & Services
SAFU is a community-owned wallet protection pool, a Soroban port of our live, audited Ethereum protocol. Members deposit into a shared pool, and if a member's wallet is drained, the pool pays them back. (This is a pool deposit, not network staking.)
Protection pool (Soroban/Rust).
 Users
deposit
 XLM into the pool through the Stellar Asset Contract; payouts stream back on-chain.
Stellar use:
 deposits, accounting, and payouts run entirely as a Soroban contract.
Impact:
 the first deterministic, drain-specific wallet protection on Stellar.
Deterministic claim oracle.
 A live off-chain scorer verifies a drain and signs a verdict the contract checks on-chain.
Stellar use:
 Soroban's Ed25519 auth verifies the signature and triggers the payout stream.
Impact:
 claims settle automatically and reproducibly, with no governance vote.
Yield via DeFindex to Blend.
 Idle pool capital is routed through DeFindex vaults into Blend lending.
Stellar use:
 reuses Stellar's largest DeFi venues instead of a custom yield contract.
Impact:
 yield is
protocol revenue, kept separate from the payout pool
, so coverage stays fully principal-backed and sustainable.
No-lock deposits with points and tiers.
 Soroban persistent storage tracks deposits, tiers, and points; require_auth gates withdrawals.
Impact:
 members stay liquid while covered, and long-term
contributors
 build standing.
Requested Budget
$30.0K
Traction Evidence
SAFU is already live and proven on Ethereum mainnet, which is the clearest evidence that the team ships and the protocol works:
Live contract:
 SAFUPool v6 at 0xB24Aee4bd963ca9f35b27fec7FbCA678d1201480, Etherscan-verified.
Live and testable end-to-end at
safustaking.com
: anyone can deposit, view a position, and exercise the deterministic claim scanner through the claim flow. The pool and the scanner that decides payouts both run in production today.
Independently audited:
 Hashlock AI audit (report:
https://aiaudit.hashlock.com/audit/890ab9ec-8311-423f-9bd1-7d4a3cce48f8
), with no critical or high findings outstanding.
Symbolically verified:
 Halmos symbolic verification, 10/10 properties, zero counterexamples.
Tested:
 194 passing tests, 98.6% coverage.
Public and usable:
 open-source at
github.com/mrkanchwala/safu-protocol
, live frontend at
safustaking.com
, ongoing updates on X at @safu_staking.
On Stellar specifically, we presented SAFU to the Stellar Türkiye community, which gives us a warm channel and guidance as we bring the protocol to Soroban.
The need is validated by Stellar's own growth: as stablecoin volume and new users move onto the chain, more value sits in wallets with no recourse if they are drained. Across DeFi, less than 2% of total value locked carries any protection, while wallet drains continue every month, and no Stellar protocol offers deterministic, drain-specific recourse today. SAFU is the team that has already built and shipped a working answer, now bringing it to Stellar.
Tranche 1 (Deliverable Roadmap) - MVP
Tranche #1 — MVP ($6,000):
- D1: Soroban ProtectionPool contract (Rust): tiered deposits, points, 30-day claim window, payout streaming (2%/day cap), solvency invariant.
Verifiable: compiles to WASM, all v6
mechanics implemented, deployed to testnet at a published contract ID on Stellar Expert; source public on GitHub.
- D2: Test suite (≥100 Rust tests) + Soroban storage/rent model + XLM calibration.
Verifiable: ≥100 passing tests with a published coverage report; storage model documented in repo.
Tranche 2 (Deliverable Roadmap) - Testnet
Tranche #2 — Testnet ($9,000):
- D1: Fraud oracle adapted to Stellar + on-chain Ed25519 verification.
Verifiable: oracle scores a Stellar tx and signs a verdict; contract verifies and a testnet claim pays out, tx
on Stellar Expert.
- D2: Yield integration (DeFindex → Blend) with claim-liquidity buffer.
Verifiable: pooled capital supplied on testnet (Stellar Expert tx), yield accrues to treasury, buffer keeps claims fundable.
- D3: Stellar frontend (Freighter / Stellar Wallets Kit): deposit, claim, status.
Verifiable: live testnet dApp (published URL) with working deposit/claim/status.
- D4:
Testnet
deploy
+
end-to-end
smoke
test
+
demo
video.
Verifiable:
public
testnet
contract
ID
+
demo
video
walking
deposit
→
points
→
claim
→
payout,
all
tx
on
Stellar
Expert.
Tranche 3 (Deliverable Roadmap) - Mainnet
Tranche #3 — Mainnet ($12,000):
- D1: Mainnet deployment + configuration.
Verifiable: contract live + verified on mainnet, address published; config tx linked.
- D2: Mainnet smoke test (full deposit-to-payout lifecycle) + demo video.
Verifiable: full lifecycle on mainnet (Stellar Expert tx hashes) + published demo video.
- D3: Security hardening + SCF Audit Bank remediation (audit funded by SCF, not billed).
Verifiable: all findings resolved, remediation commits linked.
- D4: User-testing support + fixes.
Verifiable: SCF user-testing round completed; logged issues resolved (tracker linked).
Team
Murtaza Kanchwala,
 Co-founder and protocol lead. Designed, built, and shipped SAFUPool v1 → v6 end-to-end on Ethereum mainnet. Leading the Soroban/Rust port, working in the soroban-sdk and reusing audited Stellar primitives (DeFindex, the Stellar Asset Contract) rather than building from scratch. Having shipped six production iterations of this exact system, he executes the port with low delivery risk. LinkedIn:
https://www.linkedin.com/in/mrmurtazakanchwala/
 · GitHub:
https://github.com/mrkanchwala
Deniz Köse,
 Co-founder, BD and marketing. Leads community growth, partnerships, and go-to-market, including outreach to Stellar projects and community operators, and supports product logic review and testing. LinkedIn:
https://www.linkedin.com/in/denizkosee/
Deniz
murtaza
```
