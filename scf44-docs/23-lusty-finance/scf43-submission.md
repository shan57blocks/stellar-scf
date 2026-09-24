Source: https://communityfund.stellar.org/submissions/rec9iLMyTbfXLCSlR

# Lusty: DeFi Options Yield Protocol (SCF #43, Build, status: Panel Review Failed, budget $82.5K)

Links on the page: website https://lusty.finance/, architecture https://drive.google.com/file/d/1vbqtb_sGVaPxFyLCZeIBrsdLKcvUjTK4/view?usp=sharing, GitHub https://github.com/utkurock/Lusty, video https://youtu.be/zKW65Y9YZho

## Products & Services

Lusty Finance is a non-custodial options yield protocol on Stellar. Users earn upfront yield by writing covered calls and cash-secured puts on XLM — depositing collateral and receiving a LUSD premium instantly, priced via Black-Scholes with a volatility smile adjustment. A dynamic APR engine adjusts offered rates in real time based on vault utilization, inventory exposure, and deposit flow. Settlement occurs at expiry by comparing the on-chain oracle price against the user's strike.This submission funds three development tranches: protocol hardening and completion of the put vault and position indexer (Tranche 1), decentralization via Reflector oracle integration and Soroban smart contract settlement (Tranche 2), and mainnet launch with multi-asset vault support, Aquarius ecosystem integration, and a public APR yield service (Tranche 3). By completion, Lusty will be the first trustless, oracle-driven options protocol on Stellar Mainnet.

## Traction Evidence

Lusty Finance is live on Stellar Testnet at [lusty.finance](http://lusty.finance). The protocol has processed real user deposits, option settlements, and XLM ↔ LUSD swaps — all verifiable on Stellar Testnet Horizon. Season 0 leaderboard is active with +93 registered wallets and +200 total transactions onchain.

The full options lifecycle — deposit, premium disbursement, expiry, and settlement — has been validated end-to-end on Stellar Testnet with real wallet interactions across Freighter, xBull, and Albedo. The Black-Scholes pricing engine, dynamic APR engine, and Stellar Classic settlement architecture are production-grade and running in a live environment with active users today.

## Tranche 1 (Deliverable Roadmap) - MVP

#### Server-Side Position Indexer & Automated Settlement Runner

Replace browser-based localStorage with a server-side position index

Store all position data using Horizon transaction history

Persist indexed data in PostgreSQL

Implement a scheduled settlement runner (cron job)

Automatically detect expired positions and execute settlement

Remove need for manual user claim actions

Completion Criteria:

All open positions retrievable server-side via wallet address

Users can access positions across devices

Expired positions are automatically settled within 15 minutes of expiry

Settlement results verifiable via Horizon transaction hashes

Claim history is fully trackable and auditable

#### Cash-Secured Put Vault — Completion & Stress Testing

Finalize implementation of the put vault (already partially built)

Run put vault alongside call vault under testnet conditions

Validate full lifecycle behavior in production-like scenarios

Ensure correct handling of:

Settlement logic

Collateral management

Strike assignment

Completion Criteria:

Put vault accepts LUSD deposits and distributes premiums on testnet

At least one full lifecycle executed successfully:

Deposit → Expiry → Assignment

Deposit → Expiry → Worthless outcome

Vault operates correctly under concurrent usage with call vault

All edge cases in settlement and collateral handling resolved

All actions verifiable via Horizon

#### Greeks Display & Portfolio Risk Dashboard

Expose internally calculated Greeks (delta, vega) to users

Add per-position risk metrics to the UI

Implement portfolio-level aggregation of risk metrics

Include time-to-expiry tracking per position

Completion Criteria:

Delta and vega displayed for each open position

Portfolio-level aggregation visible:

Total delta

Total vega

Time-to-expiry countdown shown per position

All displayed values match internal pricing engine outputs

Budget: $16500

## Tranche 2 (Deliverable Roadmap) - Testnet

#### Reflector Oracle Integration

Replace Binance REST API with Reflector Network (Stellar-native decentralized oracle)

Use Reflector as the sole source for:

Premium calculations

Expiry settlement pricing

Implement staleness protection mechanism

Introduce fallback state during oracle outages

Remove dependency on centralized APIs in pricing and settlement logic

Completion Criteria:

All pricing and settlement operations read from Reflector oracle feed (XLM/USD)

Staleness guard rejects outdated oracle data based on configurable threshold

Positions enter a pending state during oracle downtime

Positions become claimable once oracle feed resumes

Oracle data independently verifiable on-chain via Stellar

Integration tests cover:

Stale data scenarios

Oracle downtime handling

Recovery flow

#### Soroban Smart Contract Settlement Layer

Move settlement logic fully on-chain using Soroban smart contracts

Eliminate reliance on server-side execution and private keys

Deploy:

OptionsVault contract (position registry)

SettlementEngine contract (execution logic)

Integrate oracle-based pricing into settlement logic

Completion Criteria:

All positions recorded on-chain via OptionsVault

SettlementEngine:

Reads price from Reflector

Compares spot vs strike

Executes assignment or collateral release

Settlement transactions verifiable on-chain

Contracts deployed on Stellar Testnet

Full test suite passing

Contract source code published and reproducible

#### Mainnet Infrastructure & Security Hardening

Prepare production infrastructure for mainnet readiness

Implement monitoring, alerting, and recovery systems

Strengthen protocol security and operational reliability

Completion Criteria:

PostgreSQL production setup with:

Automated backups

Point-in-time recovery

Monitoring systems active:

Uptime monitoring

Horizon API latency tracking

Oracle feed freshness alerts

Vault utilization alerts

Full HTTP security headers implemented

Deployment and rollback procedures documented

Incident response runbook completed

Monitoring dashboards live with real-time alerts

Protocol passes all testnet checks with no critical issues remaining

Budget: $24,750

## Tranche 3 (Deliverable Roadmap) - Mainnet

#### Distributor Multi-Sig Authorization

Replace single-key distributor model with multi-signature authorization

Require M-of-N signatures for payments above configurable thresholds

Eliminate single point of failure in fund management

Secure vault withdrawals and premium distributions

Completion Criteria:

Distributor account operates under multi-sig configuration

Payments above defined threshold require multiple signatures

No single key can unilaterally withdraw funds

Multi-sig scheme tested on testnet with at least two independent signers

All authorization flows validated and secure

#### BTC Multi-Asset Expansion

Extend protocol to support BTC as an additional underlying asset

Integrate wrapped BTC issued by a trusted Stellar anchor

Connect BTC pricing to Reflector BTC/USD oracle feed

Update vault and frontend systems for multi-asset support

Completion Criteria:

BTC supported alongside XLM as collateral

BTC covered call and cash-secured put strategies functional

Pricing and settlement driven by Reflector BTC/USD feed

At least one full BTC option lifecycle completed on mainnet:

Deposit → Expiry → Settlement

BTC and XLM vaults operate independently with separate limits

#### Ecosystem Integration & Multi-Asset Vault Expansion

Expand vault infrastructure to support additional Stellar-native assets

Enable standardized onboarding for new assets

Integrate with on-chain liquidity sources (e.g. AMMs)

Improve capital efficiency and yield generation

Completion Criteria:

Vault system supports multiple assets beyond XLM and BTC

New assets configurable via:

Oracle feed

Strike parameters

Collateral type

Vault limits

Integration with Aquarius AMM for liquidity routing

LUSD trading pairs connected to on-chain liquidity

Governance proposal submitted for liquidity incentives

#### Stellar Mainnet Deployment

Deploy full Lusty protocol to Stellar mainnet

Activate production environment with real user interaction

Enable full end-to-end protocol functionality

Completion Criteria:

OptionsVault and SettlementEngine contracts deployed on mainnet

Distributor account secured via multi-sig

LUSD issuance active via protocol-controlled issuer

Initial liquidity seeded on Stellar DEX

Real user deposits and positions live

At least one complete deposit-to-settlement cycle executed on mainnet

Contracts verified and publicly accessible

Monitoring and alerting active from launch  
  
Budget: $33,000
