# Lusty Finance: Lusty: DeFi Options Yield Protocol

Source: https://communityfund.stellar.org/submissions/rec3f7RaBpmtsN85N (SCF #44, awarded $82.5K, category: Financial Protocols)

- Website: https://lusty.finance/
- Architecture doc: https://docs.google.com/document/d/1reBUmKFLi-e2t2wrnDshMFH67w4G24sp/

## Links in the submission

- https://docs.google.com/document/d/1reBUmKFLi-e2t2wrnDshMFH67w4G24sp/
- https://github.com/utkurock/Lusty

## Submission text (as published)

```text
Products & Services
Lusty is a non-custodial options yield protocol on Stellar, currently live on testnet. Users can write covered calls and cash-secured puts on XLM by depositing collateral, selecting a strike price and weekly expiry, and instantly receiving the option premium upon deposit. Pricing and settlement are handled by a single server-canonical engine, while settlement references the Reflector oracle. The Soroban vault contract is already deployed and has successfully settled real positions on testnet, although the current web product still relies on server-side settlement rails.
This grant will transition Lusty from a server-settled product into a fully on-chain, oracle-settled, multi-asset protocol. The following features are grant deliverables and represent the roadmap beyond the current implementation:
On-chain custody vault
Today, collateral is escrowed through a server distributor account. The grant will migrate deposits and claims to the deployed Soroban contract, enabling the contract itself to hold collateral, pay premiums atomically during deposits, and release funds during settlement.
Stellar components: Soroban smart contracts and Stellar asset transfers.
Impact: removes the final trust assumption and makes Lusty fully trustless rather than conditionally trustless.
Automated, permissionless settlement
The grant will introduce a scheduled runner that calls the contract’s permissionless
settle()
 function on expired positions. The contract will read the Reflector oracle price pinned to the expiry ledger and settle positions deterministically.
Stellar components: Reflector oracle integration and Soroban automation.
Impact: eliminates manual claim flows and removes admin intervention from the settlement path.
BTC and multi-asset vaults
The protocol will expand beyond XLM to support covered calls and cash-secured puts on BTC, alongside a framework for additional assets in the future.
Stellar components: Reflector BTC/USD oracle feed and wrapped BTC issued through a Stellar anchor.
Impact: introduces multiple yield markets, broadening TVL potential and attracting a wider user base.
Ecosystem liquidity integrations
The grant will connect LUSD/USDC vault flows with on-chain liquidity across Stellar ecosystem protocols.
Stellar components: Stellar DEX and ecosystem AMMs.
Impact: improves liquidity depth and increases capital efficiency for users.
Mainnet launch
As the final milestone, XLM and BTC strategies will launch on mainnet, settling in Circle’s native USDC on Stellar. Distributor and issuer accounts will operate under multisig security.
Stellar components: native USDC on Stellar and account-level multisig.
Impact: transforms a testnet protocol with 211 wallets into a production-ready, real-money yield protocol.
Requested Budget
$82.5K
Traction Evidence
Lusty is live on Stellar testnet at
lusty.finance
. The full options lifecycle (deposit, premium disbursement, expiry, settlement) runs end to end through real wallets on Freighter, xBull and Albedo, and every transaction is verifiable on Horizon.
First month on testnet:
  - 211 wallets
  - 275,900 points awarded through the Season 0 leaderboard
  - $1.5K in premiums paid upfront to depositors
Tranche 1 (Deliverable Roadmap) - MVP
Milestone 1: On-Chain Custody and Core Vault Migration
Move user collateral off the server-operated distributor rail and into the deployed Soroban OptionsVault contract. Deposits, collateral escrow, premium payments, position records, and settlement claims all become contract operations instead of server-side ones. The contract also gets extended to handle both covered calls and cash-secured puts, each with its own collateral requirements, plus the vault-level checks that go with that: collateralization, solvency, position size, and exposure.
Completion Criteria:
Users open covered-call and cash-secured-put positions directly from their Stellar wallets.
The Soroban contract holds active collateral, not the server distributor account.
Opening a position pays the premium atomically, in the same transaction.
Anyone can verify position parameters and collateral balances from contract state.
The server has no path to withdraw or redirect collateral tied to an active position.
Covered calls and cash-secured puts each enforce their own on-chain collateral rules.
Budget:
 $7,000
Milestone 2: Secure Quoting and Permissionless Settlement
Add a signed-quote system keyed to authorized quoters. The contract validates every quote before accepting it, checking premium ceilings, expiration, replay protection, vault capacity, and exposure limits so invalid or malicious quotes never settle. Quoter management and any remaining operational accounts move under multisig, with the policies written down.
A scheduled runner handles settlement. It calls the contract's permissionless settle() on expired positions, and that is the limit of what it can do: it cannot set the settlement price, change position parameters, or touch user collateral.
Completion Criteria:
The contract accepts only quotes signed by an authorized quoter.
Expired or already-used quotes get rejected.
Premium ceilings and vault-risk limits hold on-chain, not in application code.
Adding or removing a quoter takes multisig approval.
No remaining operational account custodies active user collateral.
Anyone can settle an expired position through settle().
The runner settles eligible expired positions on its own, with no manual claim from the user.
Settlement prices come from the configured Reflector oracle feed and are verifiable on-chain.
Budget:
 $5,500
Milestone 3: Portfolio Risk and Production Interface
Build out portfolio-level monitoring and risk analytics in the application. This adds Greeks exposure (delta and vega) and wires the dashboard to Lusty's production pricing engine, so the displayed numbers match what the engine actually outputs. Each active position shows up alongside its collateral, premium income, expiry, settlement status, and asset-level exposure.
The documentation covers the full picture: application flow, the pricing engine, the signed-quote system, the Soroban vault, the Reflector oracle integration, and how settlement works.
Completion Criteria:
Users can see every active and settled position in the application.
The dashboard shows collateral, premium income, expiry, settlement status, and asset-level exposure.
Delta and vega match the production pricing engine's output.
Application data stays in sync with on-chain position state.
The documentation spells out what each protocol component is responsible for and where its security boundary sits.
The full deposit-to-settlement lifecycle is documented and demonstrated on Stellar testnet.
Budget:
 $4,000
Tranche 1 Total:
 $16,500 — 20%
Tranche 2 (Deliverable Roadmap) - Testnet
Milestone 1: BTC Options Integration
Add BTC as a second underlying alongside XLM, using wrapped BTC issued through a Stellar anchor. BTC positions price and settle off the Reflector BTC/USD feed. Once that is in place, users can write BTC covered calls and cash-secured puts, with BTC kept on its own books: its own asset config, collateral rules, exposure limits, and vault capacity, fully separate from XLM.
Completion Criteria:
BTC runs as the second supported underlying next to XLM.
At least one full BTC covered-call lifecycle completes on testnet, end to end.
At least one full BTC cash-secured-put lifecycle completes on testnet.
Each of those lifecycles covers the whole path: deposit, premium payment, expiry, oracle-priced settlement, and collateral distribution.
BTC settlement prices come from the configured Reflector BTC/USD feed.
The transactions and settlement outcomes are verifiable on-chain.
BTC and XLM keep separate vault accounting and risk limits.
Budget:
 $8,250
Milestone 2: Multi-Asset Framework and Liquidity Routing
Generalize the vault into a reusable multi-asset framework, so adding a new asset becomes a configuration step instead of a rewrite of the settlement core. Each asset is described by its own config:
Contract address
Oracle feed
Settlement asset
Strike parameters
Expiry parameters
Collateral requirements
Position limits
Vault capacity
Exposure limits
On the liquidity side, wire LUSD and USDC vault flows into on-chain liquidity from Stellar ecosystem protocols. Routing stays inside guardrails: approved routes only, slippage limits, minimum-output requirements, and explicit handling for when a transaction fails.
Completion Criteria:
The multi-asset framework is live on testnet.
Both XLM and BTC run through that same framework, not separate one-off code paths.
The framework enforces each asset's own oracle, collateral, settlement, and risk parameters.
LUSD and USDC liquidity routing works on testnet.
Slippage limits and minimum-output protections hold.
A failure in external liquidity never changes the settlement terms of an open option position.
The multi-asset configuration and liquidity-routing docs are published.
Budget:
 $8,250
Milestone 3: Adversarial Testnet Program
Run a structured adversarial program against the testnet build, with a public, open attack window. Lusty already runs on Stellar testnet, so this is an open invitation to try to break the Tranche 2 release candidate once it ships into that live environment, not a first-time deployment.
The scope is deliberately wide. It covers attempts to:
reach collateral without authorization
submit invalid or replayed signed quotes
act as a compromised or malicious quoter
get past premium ceilings
get past position and exposure limits
push the vault into insolvency or undercollateralization
force the wrong oracle feed
mishandle the expiry price
settle in-the-money or out-of-the-money positions incorrectly
double-settle or claim the same position twice
escalate administrative permissions
break BTC/XLM vault isolation
corrupt multi-asset configuration
exploit liquidity-routing slippage or transaction failure
interrupt the settlement runner
exploit an unavailable oracle or external integration
The attack window stays open for at least 14 consecutive days, starting once the Tranche 2 release candidate is promoted to our live Stellar testnet. Before it opens, Lusty publishes the testnet contract addresses, the testing scope, rules of engagement, the reporting channel, known limitations, and step-by-step instructions for reproducing each protocol flow.
Severity Definitions:
Critical:
 unauthorized withdrawal, permanent loss of collateral, arbitrary contract control, systemic insolvency, or invalid settlement across multiple positions.
High:
 a bug that materially misprices positions, bypasses a core risk control, compromises a privileged role, prevents correct settlement, or puts a meaningful amount of collateral at risk.
Medium:
 limited financial impact, temporary disruption, or wrong behavior under specific conditions, without immediate systemic loss.
Low / Informational:
 no direct financial impact: documentation gaps, usability issues, monitoring improvements, and general hardening suggestions.
Definition of Resolved:
 A finding counts as resolved only when all of these hold:
We have identified the root cause.
A fix is in place, whether in code, configuration, or architecture.
A regression or reproducible test now guards against it.
The updated build is redeployed to testnet.
The original attack or failure scenario has been retried and can no longer be reproduced.
Completion Criteria:
The attack window stayed open for at least 14 consecutive days.
Every submitted finding is triaged and documented.
No Critical or High-severity finding remains unresolved.
Each resolved Critical or High finding has been retested.
Medium findings are either fixed or carry a documented mitigation and a remediation deadline set before mainnet.
Nothing known remains that could cause unauthorized collateral loss, systemic insolvency, settlement manipulation, or a bypass of core risk controls.
A final adversarial testnet report is published: scope, findings, severity, remediation status, and any accepted residual risk.
Budget:
 $8,250
Tranche 2 Total:
 $24,750 — 30%
Tranche 3 (Deliverable Roadmap) - Mainnet
Milestone 1: Security Review and Mainnet Readiness
Put the final mainnet release-candidate contracts and infrastructure through a formal security review. Qualified reviewers run it as part of the project's security process, and the review reaches across the whole system:
Collateral custody and withdrawal authorization
Covered-call accounting
Cash-secured-put accounting
Signed-quote verification
Signature replay protection
Quote expiration
Quoter permissions
Premium ceilings
Vault collateralization and solvency
Position and exposure limits
Reflector oracle integration
Expiry and settlement-price selection
Permissionless settlement
Double-settlement prevention
Multi-asset vault isolation
Asset decimal and precision handling
Administrative and multisig permissions
Emergency pause controls
Liquidity-routing boundaries
Deployment procedures
Scenarios where the oracle, runner, or an external integration is unavailable
The release candidate does not move to an unrestricted public mainnet launch while any Critical or High-severity finding is still open.
Completion Criteria:
The final mainnet release candidate goes through a formal security review.
Every finding is documented and assigned a severity.
All Critical and High-severity findings are remediated.
Each Critical and High remediation ships with a regression test.
Those remediations are retested and confirmed resolved.
Medium-severity findings are either fixed or carry a documented mitigation and remediation plan.
A security summary captures the reviewed version, scope, findings, and remediation status.
Any material code change made after the review is documented and re-reviewed before launch.
Budget:
 $10,000
Milestone 2: Mainnet Contract Deployment
Deploy the production contracts to Stellar mainnet: the multi-asset vault framework from Tranche 2 and the settlement engine that drives it. The launch supports XLM and BTC covered calls and cash-secured puts, with premiums and settlement denominated in Circle's native USDC on Stellar. Production configuration covers the oracle feeds, settlement assets, authorized quoters, vault and exposure limits, and the multisig-controlled administrative roles. Limits start conservative and asset-specific.
Completion Criteria:
The production contracts are deployed and verified on Stellar mainnet.
The mainnet contract addresses are published.
XLM and BTC are both configured as supported underlyings.
Covered calls and cash-secured puts run for both assets.
Circle's native USDC on Stellar is the main settlement asset.
The approved Reflector oracle feeds are configured and verified.
Administrative, operational, distributor, issuer, and emergency-control accounts all sit under documented multisig control.
Vault-capacity, position-size, open-interest, and asset-exposure limits are live.
The deployed configuration matches the reviewed release candidate, with no drift.
At least one controlled end-to-end cycle runs on mainnet: deposit, premium payment, expiry, oracle settlement, and collateral distribution.
Budget:
 $18,000
Milestone 3: Controlled Public Launch and Monitoring
Open real user positions on mainnet under conservative initial limits. Seed the protocol's starting liquidity and bring production operations online: monitoring, alerting, incident response, and backup settlement. Before access opens up without restriction, finish professional user testing and clear the production-readiness checklist.
Production monitoring watches:
Vault solvency
Oracle health
Settlement failures
Settlement-runner availability
Abnormal position activity
Multisig and administrative actions
Liquidity-routing failures
Infrastructure availability
Completion Criteria:
Professional user and acceptance testing is done.
The production deployment checklist is complete.
Initial liquidity is live.
Real user positions are open under conservative limits.
Vault-solvency and oracle-health monitoring are both running.
Settlement-failure alerts are firing.
Infrastructure and settlement-runner monitoring are in place.
Incident-response and emergency-pause procedures are documented and operational.
Backup settlement procedures have been tested.
No Critical or High-severity finding is open at public launch.
The first mainnet settlement cycles complete cleanly before any limits are raised.
Budget:
 $13,250
Tranche 3 Total:
 $41,250 — 50%
Team
Utku Kaya (@Utkurocks), Protocol Lead and Lead Contract Engineer
Web3 and game developer, creator of FashAI a decentralized fashion prediction market with a Web3-integrated mobile app and founder of Doubt Prediction Market, a platform where users create custom markets, provide liquidity, and earn revenue. He built Lusty’s pricing engine, led the security rewrite, and developed the deployed Soroban vault contract.
Utku has experience shipping across Stellar, Solana, and EVM ecosystems, with a focus on DeFi derivatives, prediction market infrastructure, skill-based Web3 gaming, and consumer dApps. He owns the Soroban contract architecture, settlement engine, and pricing systems across every project tranche.
X:
https://x.com/Utkurocks
Github:
https://github.com/utkurock
LinkedIn:
https://www.linkedin.com/in/utkukaya-/
Lukas Çakıcı (@lukascakici), Full-Stack Developer
Computer engineer and former Director of Software at Bilgi Blockchain, currently working as a software engineer at Vodafone building cross-platform mobile tooling. Lukas develops responsive interfaces for both decentralized applications and enterprise systems using React and React Native. Within Lusty, he owns the frontend architecture, Greeks and risk dashboard, and wallet integrations across Freighter, xBull, and Albedo. This division allows protocol and settlement work to progress independently from the application layer.
X:
https://x.com/lukascakici
Github:
https://github.com/lukascakici
LinkedIn:
https://www.linkedin.com/in/lukascakici/
Mert Kaya (@web3catalysor), Backend Engineer and Ecosystem Lead
Cofounder, CEO, and lead developer at Blockera Corp, with participation in Founder Institute, Connected, Techstars-affiliated programs, Cardano Catalyst, and the Hedera ecosystem grant program.
Mert focuses on backend infrastructure, ecosystem integrations, and coordination required for Stellar mainnet deployment and liquidity partnerships.
X:
https://x.com/web3catalysor
Kerem Ozcoban (@tulkas)
Web3 business development and ecosystem growth operator with over five years of experience across multiple market cycles. As a strategic lead at growth agencies, he led campaigns for Aptos, Orochi Network, Saros, and Nesa, while also contributing to growth initiatives at Binance that increased user participation.
Most recently, he led user acquisition efforts for a perpetual DEX prior to its acquisition by Rise Chain. His work spans institutional partnerships, decentralized AI growth, and ecosystem expansion strategies.
X:
https://x.com/tulkas
Barış Nalbantlar (@AztecVe)
Web3 growth and ecosystem operator specializing in user acquisition, trading growth, NFT ecosystems, and community activation. He has led campaigns across multiple Web3 ecosystems, including collaborations with
io.net
 and regional ecosystem expansion across Turkey and MENA.
In 2021, he ran KOL-driven campaigns with Hacken for early-stage community growth. He later scaled BSX (Rise Chain) perpetual DEX daily trading volume from $16M to $113M through the Lilquid NFT campaign and KOL-driven expansion. Between 2021 and 2023, he worked within the Vecahin network, launching a network-backed NFT project and managing event operations during UFC events across the MENA region.
X:
https://x.com/AztecVe
LinkedIn:
https://www.linkedin.com/in/baris-nalbantlar/
Lukas Çakıcı
kerem
Utku Kaya
Baris nalbantlar
Mert Kaya
```
