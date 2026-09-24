# Etesia: Risk parity vaults & perp CTA on Stella

Source: https://communityfund.stellar.org/submissions/recuUVlYWqrAk5t9Z (SCF #44, awarded $113.0K, category: End-User Application)

- Website: https://portfolio.etesiar.com/
- Architecture doc: https://drive.google.com/file/d/1UFbmHJhViw-Jee8Zq4O7ots8-enPfmWF/view?usp=sharing

## Links in the submission

- https://drive.google.com/file/d/1UFbmHJhViw-Jee8Zq4O7ots8-enPfmWF/view?usp=sharing
- https://drive.google.com/drive/folders/1nTWFH6vzMwose8P1IvOJJkPZPCJxqAGF?usp=sharing
- https://www.youtube.com/watch?v=e9HTVoKcmGQ
- https://www.youtube.com/watch?v=bIOcZfIbrZk

## Submission text (as published)

```text
Products & Services
This submission brings non-custodial, systematic asset management to Stellar by integrating four protocols from the SCF Integration List. Etesia already runs a live systematic vault on Hyperliquid; this project ports and extends that infrastructure to Stellar.
We are building two things:
A tool that helps users construct and allocate a diversified, risk-balanced portfolio.
The diversifying products to put in it: our own systematic trend-following vaults.
Real diversification needs return streams that are not correlated. On Stellar today, the investable universe is almost entirely directional crypto beta, with most assets moving alongside Bitcoin. Trend-following supplies what is missing: it carries a structurally low and often negative correlation to buy-and-hold crypto (our live strategy runs near -0.22 to Bitcoin).
The vaults are the diversifying building blocks; the Portfolio Builder is the free tool that allocates across them.
We deliver two vaults and one tool.
1. Long-Only Trend-Following Vault (Soroswap)
A spot trend-following vault over Stellar assets: long while an asset trends up, rotated into stablecoins otherwise.
All execution runs through Soroswap, whose routing API aggregates Soroban and classic liquidity. Stablecoins held during flat phases earn yield in Blend v2 lending pools.
This strategy requires only spot DEX liquidity, not perpetuals, allowing it to launch first.
How Stellar is used
Soroswap (TWAP pricing and swap execution)
Blend v2 (stablecoin yield)
SEP-56 vault with SEP-41 shares on Soroban
Reflector as a price circuit breaker
Ecosystem impact
Recurring organic swap volume into Soroswap on every rebalance
Systematic lending supply into Blend v2
2. Long-Short Trend-Following Vault
A full Commodity Trading Advisor (CTA) strategy, long and short, porting the strategy Etesia already runs live on Hyperliquid. What launches on Stellar is proven rather than experimental.
The strategy is built in four stages:
Portfolio construction allocates the risk budget strategically across products.
Multiple proprietary oscillators capture trends on each product.
Positions are sized under explicit cost and risk controls.
An in-house execution engine handles trade execution.
The long-short component is built behind a venue-agnostic execution interface. At runtime, it connects to one of two equivalent execution paths:
(a) Native perpetuals — via whichever Stellar perp venue reaches mainnet first. win-trader (formerly
stellars.finance
, SCF grant delivered, in final audit, mainnet planned before September 2026) is the primary candidate; Zenex and Noether are monitored as alternatives.
(b) Synthetic shorts via Blend v2 + Soroswap — constructed atomically: deposit collateral on Blend, borrow the target asset, swap via Soroswap. This produces equivalent short exposure with effective leverage through recursive collateralization, at a carry cost economically comparable to perp funding rates.
Both paths sit behind the same interface. The signal engine, risk controls, and position sizing are identical regardless of which path is used.
How Stellar is used:
Native perp venue (win-trader) or synthetic shorts via Blend v2 + Soroswap for custody and settlement
Ecosystem impact:
Professional systematic trading volume on Stellar; synthetic short path deepens Blend v2 and Soroswap integrations
3. Portfolio Builder
A non-custodial allocation tool that enables users to build diversified, risk-balanced portfolios across Etesia vaults and other Stellar assets using our risk parity engine.
Custody remains in the user's wallet and underlying vaults. The Portfolio Builder only computes and routes allocations.
Etesia vaults expose the DeFindex Strategy interface, allowing any DeFindex-integrated wallet or application to offer them without additional integration work.
How Stellar is used
Stellar Wallets Kit (Freighter, xBull) for connectivity and trustline setup
DeFindex for composability
Ecosystem impact
Makes risk-balanced portfolio construction a reusable building block for the entire Stellar ecosystem
Shared Architecture
Across all three products, the principle is the same:
Off-chain intelligence, on-chain custody and settlement.
The quantitative engine only proposes actions. Each vault independently verifies every rebalance against on-chain invariants, including:
Asset allowlists
Slippage bounds
Notional caps
Cooldowns
A compromised backend cannot move user funds. In the worst case, a rebalance is missed or delayed.
Requested Budget
$113.0K
Traction Evidence
Etesia has already deployed a live systematic investment strategy through a non-custodial tokenized vault on Hyperliquid, providing initial validation of its quantitative investment framework.
Key traction highlights:
Founding team combines institutional portfolio management, systematic trading and blockchain engineering expertise, including experience from a FINMA-regulated CTA managing approximately $400M AUM.
Live trend-following strategy on Hyperliquid. The vault NAV is computed on-chain and displayed by Lagoon, an independent third-party platform. The ~13% figure was the high-water mark; the vault currently sits around 5% since inception — normal behaviour for a trend-following strategy.
Full track record verifiable at:
Vault contract (HyperEVM): 0xF99aC94E1630a28D4Cb8d340efE16303933db63b
Lagoon UI:
https://app.lagoon.finance/vault/999/0xb718bdaa857d5ab82c09c7f0c75bfba2f831090a#details
Dashboard:
https://app.etesiar.com/
Backtested performance shows a 1.40 Sharpe ratio, 34.6% annualized return, 9.3% maximum drawdown and -0.22 correlation to Bitcoin.
Risk parity engine and Portfolio Builder under active development.
Advanced discussions with three fund-of-funds representing approximately $600K of initial AUM, with plans to scale toward multi-million-dollar allocations.
��� Planned deployment on Stellar through SEP-56 tokenized vault infrastructure.
TRACTION EVIDENCES :
LOIs
Etesia main website :
https://www.etesiar.com/
Vision explained :
https://www.youtube.com/watch?v=e9HTVoKcmGQ
POC on Stellar - Portfolio manager :
https://portfolio.etesiar.com/
Demo of POC :
https://www.youtube.com/watch?v=bIOcZfIbrZk
Research Article :
Trend Following – Systematic Macro Trading
Tranche 1 (Deliverable Roadmap) - MVP
Deliverable 1: Trend-Following & Allocation Engine + Backtesting
Description
Build the production Python engines:
The trend-following signal engine for the vaults (multiple proprietary oscillators producing long/flat positions per asset)
The risk parity allocation engine that powers the Portfolio Builder (blended fast/slow EWMA covariance with shrinkage, Equal Risk Contribution solver, concentration caps)
Includes the market data pipeline for the Stellar asset universe and a reproducible backtesting framework with walk-forward validation.
How to measure completion
Trend signals and risk parity allocations reproducible with documented parameters and data sources
Backtests reproducible; risk contributions equal within a documented tolerance
Engine outputs long/flat targets for a Top-N Stellar basket and risk parity weights for the builder
Budget:
 $15,800
Completion Date:
 29.07.2026
Deliverable 2: SEP-56 Vault Contracts & On-Chain Guardrails
Description
Develop and unit test Soroban smart contracts implementing SEP-56 compliant non-custodial vaults that hold the trend-following strategy:
Deposit, withdraw, mint and redeem flows
NAV-based share accounting
Role separation (user, executor, guardian, admin)
An
execute_rebalance
 entrypoint validating structured rebalance plans against on-chain invariants:
Asset allowlist
Per-leg
minAmountOut
 slippage bounds
Notional limits
Cooldowns
Emergency pause
How to measure completion
Contracts compile without warnings using
stellar-cli
90%+ unit test coverage including invariant tests
Tests validate share mint/burn correctness and rejection of plans violating guardrails
Successful local deployment and interaction via Soroban RPC
Budget:
 $16,400
Completion Date:
 12.08.2026
Deliverable 3: Soroswap TWAP Pricing & Reflector Circuit Breaker
Description
Implement the dual-layer pricing used for NAV computation and execution bounds:
Time-weighted average prices read from allowlisted Soroswap pools as the on-chain execution reference for
min_amount_out
 bounds
Reflector as a deviation circuit breaker
Execution halts on stale or diverging feeds.
Soroswap is the pricing backbone; the executor never supplies prices.
How to measure completion
Soroswap TWAP returns validated prices for the full MVP asset universe
Reflector circuit breaker halts execution on deviation or staleness
NAV and share price update correctly from validated prices
Budget:
 $9,600
Completion Date:
 26.08.2026
Tranche 2 (Deliverable Roadmap) - Testnet
Deliverable 4: Soroswap Swap Execution + Blend v2 Yield on Testnet
Description
Deploy the off-chain orchestrator connecting the trend-following engine to the on-chain vault:
Scheduled and signal/drift-triggered rebalancing
Target weights published on-chain prior to execution
Swap execution routed through the Soroswap routing API (aggregating Soroban and classic liquidity), with Phoenix as a secondary path
When the strategy goes flat, stablecoins are deposited into Blend v2 lending pools via cross-contract call and withdrawn when it re-enters, allowing idle cash to earn yield that accrues to vault NAV.
How to measure completion
End-to-end rebalances executed on Stellar testnet through Soroswap and verifiable on-chain
Drift and signal triggers fire correctly under simulated market moves
Idle stablecoins deposited into a Blend v2 testnet pool; yield accrual reflected in vault NAV
Plans violating vault guardrails are rejected on-chain
Budget:
 $12,700
Completion Date:
 09.09.2026
Deliverable 5: Portfolio Builder Tool & Stellar Wallets Kit
Description
Ship the Portfolio Builder web tool on testnet:
Vault catalog with live allocations and risk metrics
Allocation flow enabling users to build a risk-balanced portfolio across Etesia vaults and Stellar assets
Deposit and withdrawal flows
Wallet connectivity is provided through Stellar Wallets Kit (Freighter, xBull), including automatic trustline setup for SEP-41 share tokens.
An event indexer computes TVL, allocation history, and performance metrics, which are served through an API.
How to measure completion
End-to-end testnet flow from Stellar Wallets Kit connection to allocation, deposit, and withdrawal
Indexed metrics displayed in the dashboard with sub-second retrieval for standard views
Rebalance events and Blend yield reflected in the UI from indexed data
Budget:
 $13,000
Completion Date:
 23.09.2026
Deliverable 6: Stress Testing, Monitoring & Mainnet Readiness
Description
Harden the system ahead of mainnet launch:
Simulated stress scenarios (volatility spikes, liquidity gaps, oracle staleness, keeper downtime)
Monitoring and alerting on vault state and executor liveness
Operational runbooks
Final calibration of rebalance thresholds, slippage caps, and concentration limits
How to measure completion
Stress scenarios executed with documented results; invalid executions rejected on-chain
Monitoring and alerting operational for all testnet vaults
Mainnet deployment checklist completed and reviewed
Budget:
 $10,400
Completion Date:
 07.10.2026
Tranche 3 (Deliverable Roadmap) - Mainnet
Deliverable 7: Mainnet Launch + DeFindex Strategy Interface
Description
Deploy the SEP-56 vault contracts to Stellar mainnet, launch the production Portfolio Builder, and open the first long-only trend-following vault with Soroswap rebalancing and Blend v2 yield live.
Expose the DeFindex Strategy interface (
invest
,
unwind
,
harvest
) so Etesia vaults become composable as DeFindex strategies, allowing any DeFindex-integrated wallet or application to access them without additional integration work.
How to measure completion
Contracts deployed on Stellar mainnet with public addresses and verified source code
Deposits, withdrawals, and rebalances executed by real accounts from the live UI
DeFindex Strategy interface callable and tested against a DeFindex vault on testnet
Live vault metrics visible in the production dashboard
Budget:
 $11,900
Completion Date:
 21.10.2026
Deliverable 8: Long-Short Execution Infrastructure – Market Data, Volatility & Volume Models
Description
Build the venue-agnostic quantitative infrastructure for the long-short vault, targeting whichever execution path offers the best liquidity at Tranche 3: native perpetuals (win-trader as primary candidate) or synthetic shorts via Blend v2 + Soroswap.
This includes:
Market data pipeline: perp venue connectors (order book, trades, funding, mark and index prices) and/or Blend pool state monitoring (utilization, borrow rates, available liquidity) and Soroswap pool data
Data normalization, persistence, and automated gap backfill
Multi-horizon volatility models for position sizing and risk targeting
Volume and liquidity models, including depth profiling, participation caps, and market impact estimation
The objective is to safely size and execute orders on a young venue or through synthetic construction with evolving liquidity.
How to measure completion
Market data captured and persisted continuously on at least one execution path (native perp or synthetic short) with automated gap detection
Volatility and volume model outputs validated against recorded data
Position sizing pipeline produces bounded target positions respecting path-specific constraints
Budget:
 $12,000
Completion Date:
 04.11.2026
Deliverable 9: Long-Short Trend-Following Vault Launch
Description
Port our live Hyperliquid long-short trend-following strategy to Stellar and launch a depositor-facing vault. Execution via native perp venue (win-trader) or synthetic shorts (Blend v2 + Soroswap), depending on venue availability and liquidity at launch.
Adapt the Python signal and execution stack, including:
Order placement and amendment (perp path) or atomic collateral/borrow/swap construction (synthetic path)
Order slicing within participation limits
Fill tracking and position reconciliation
Drawdown guards and kill switch mechanisms (health factor monitoring for synthetic path)
Trading runs through our off-chain infrastructure, while deposits, accounting, and settlement are handled on Stellar through the venue's vault primitive or an Etesia SEP-56 vault.
How to measure completion
Execution engine runs continuously on at least one execution path (native perp or synthetic short) with verifiable order and fill history
Vault open to external depositors and trading the long-short strategy on mainnet
Live monitoring reports positions, PnL, and risk in real time
Budget:
 $11,200
Completion Date:
 15.11.2026
Team
Arnaud Dahan
Position:
 Co-founder, Portfolio Manager
Expertise:
 Quantitative finance, systematic portfolio management, risk parity strategies
Experience:
 Former TradFi systematic portfolio manager and quantitative researcher. Managed institutional capital in hedge fund environments with combined team AUM of $400M.
LinkedIn:
https://www.linkedin.com/in/arnauddahan/
Cedric Lewis
Position:
 Co-founder, COO & Project Manager
Expertise:
 Operational coordination, multi-stakeholder programmes, business development
Experience:
 Former pharmaceutical scientist with MBA, sales, business development and consulting experiences.
LinkedIn:
https://www.linkedin.com/in/cedric-lewis-10339576/
Thibault Dahan
Position:
 CTO / Full-stack & Blockchain Developer
Expertise:
 Full-stack development, blockchain engineering, 3D applications
Experience:
 Built
CustomHome.ai
 (3D real estate visualization platform) and a 3D printer marketplace. Brings deep technical experience across web and blockchain development.
LinkedIn:
https://www.linkedin.com/in/thibault-dahan-5a37ab128/
GitHub:
https://github.com/thibo-grigalr
Fabrice Cohen
Position:
 Engineer, Quant Infrastructure & Backend
Expertise:
 Systematic trading, quantitative strategies, risk management
Experience:
 Former TradFi systematic trader with extensive institutional trading experience. Part of the team's combined $400M AUM track record managing hedge fund capital. Hands-on Rust experience. Contributing across quant infrastructure and backend on this project.
LinkedIn:
https://www.linkedin.com/in/fabrice-cohen-2348722/
Riad Lemhachheche
Position:
 Engineer, Backend & Integration
Expertise:
 Blockchain engineer, business development, partnerships
Experience:
 Contributing across backend and integration work on this project. Renowned blockchain advisor who advises multiple blockchain projects. Brings strong industry network and go-to-market expertise.
LinkedIn:
https://www.linkedin.com/in/riadlem/
GitHub :
https://github.com/riadlem
Arnaud
```
