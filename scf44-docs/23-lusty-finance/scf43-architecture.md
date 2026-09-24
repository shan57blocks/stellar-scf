Source: https://drive.google.com/file/d/1vbqtb_sGVaPxFyLCZeIBrsdLKcvUjTK4/view?usp=sharing (PDF, text extracted; architecture doc linked from the SCF #43 submission)

TECHNICAL ARCHITECTURE DOCUMENT
Lusty
DeFi Options Yield Protocol on Stellar
Stellar Testnet (Classic)
STACK
Next.js 14 · TypeScript · PostgreSQL
PRICING
Black-Scholes with Volatility Smile
APR
Dynamic Engine — Utilization / Inventory / Flow
VERSION
v0.1  Season 0
DATE
April 2026
lusty.finance

Lusty Finance — Technical Architecture
Page 2
01
Overview
Lusty is a non-custodial options yield protocol on Stellar Classic. Users earn upfront
yield by writing covered calls (deposit XLM, receive LUSD premium) and cash-secured puts
(deposit LUSD, receive LUSD premium) on XLM. Premiums are priced via Black-Scholes with a
volatility smile adjustment and disbursed immediately upon deposit verification.
Settlement occurs server-side at expiry, comparing the Binance spot price against the
user-selected strike.
Product type
Options yield — Covered Calls and Cash-Secured Puts on XLM
Underlying
XLM/USD (Binance REST at deposit, WebSocket for live display)
Settlement
Server-side at expiry — spot-vs-strike comparison
Collateral
XLM for calls, LUSD for puts — held in distributor account
Premium split
User receives 75% of Black-Scholes fair value; 25% protocol fee
Expiries
Short-dated rolling windows — generated dynamically
Network
Stellar Testnet (Horizon API, Stellar SDK, no smart contracts)
02
System Architecture
The system is a monolithic Next.js 14 application. API route handlers serve as the
financial backend; there are no smart contracts. All state transitions are validated
against the Stellar Horizon API before funds are disbursed.
2.1  Layer Map
Layer
Technology
Responsibility
Presentation
Next.js 14 · React 18 · Tailwind CSS
Strike picker, position management, swap UI
Wallet
Stellar Wallets Kit
(Freighter/xBull/Albedo/Lobstr)
Key management, transaction signing
API
Next.js Route Handlers (Node.js)
Premium calc, settlement, leaderboard
Pricing
src/lib/pricing.ts +
src/lib/aprEngine.ts
Black-Scholes, vol smile, dynamic margin
Blockchain
Stellar Classic — Horizon v2 REST
Tx verification, payments, path-payment
swaps
Database
PostgreSQL via Supabase (connection
pooled)
Transactions, users, leaderboard, desk notes
Price feed
Binance REST (server) + WebSocket
(client)
Live XLM/USD spot for pricing and settlement
2.2  Deposit and Premium Flow
1. User signs XLM or LUSD payment -
> broadcasts to Stellar network
2. POST /api/vault/deposit
| verify tx hash on Horizon (amount, asset, memo, sender)
| fetch XLM/USD spot from Binance
| compute Black-Scholes fair_value with smile-adjusted IV

Lusty Finance — Technical Architecture
Page 3
| apply dynamic margin -
> user_premium = fair_value x 0.75
|
|-
> distributor sends user_premium (LUSD) to user wallet
|-
> distributor sends fee (LUSD) to fee wallet
|-
> transaction logged to PostgreSQL
'-
> position written to browser localStorage
2.3  Settlement Flow
POST /api/vault/claim (called by user at or after expiry)
| validate position ownership
| fetch current XLM/USD spot from Binance
|
| Covered Call:
| spot < strike -
> return XLM collateral (option expired worthless)
| spot >
= strike -
> assign at strike price (XLM converted to LUSD value)
|
' Cash-Secured Put:
spot > strike -
> return LUSD collateral (option expired worthless)
spot <
= strike -
> assign at strike price (send XLM at put-strike to user)
03
Options Pricing Engine
All pricing is computed server-side in 
src/lib/pricing.ts
. The model uses closed-form
Black-Scholes extended with a quadratic volatility smile to capture the skew typical in
short-dated crypto options.
3.1  Black-Scholes Formula
Inputs:
S = spot price (XLM/USD from Binance at deposit time)
K = strike price (user-selected from generated grid)
r = risk-free rate (constant: 0.05)
IV = implied volatility (base IV adjusted by smile, see 3.2)
T = time to expiry (years)
d1 = ( ln(S/K) + (r + IV^2 / 2) * T ) / ( IV * sqrt(T) )
d2 = d1 - IV * sqrt(T)
Call premium = S * N(d1) - K * exp(-r*T) * N(d2)
Put premium = K * exp(-r*T) * N(-d2) - S * N(-d1)

Lusty Finance — Technical Architecture
Page 4
N(x) = standard normal CDF (rational approx, error < 7.5e-8)
3.2  Volatility Smile Adjustment
moneyness = ln(K / S) // log-moneyness
smile_k = 6.0 // curvature coefficient
IV_eff = IV_base * ( 1 + smile_k * moneyness^2 )
// ATM strikes use IV_base unchanged.
// OTM and ITM strikes receive progressively higher IV_eff,
// producing higher premiums for tail-risk strikes.
3.3  Strike Grid
Covered Calls
102%, 106%, 112%, 120% of spot — OTM above market
Cash-Secured Puts
80%, 88%, 94%, 98% of spot — OTM below market
Tick rounding
Strikes normalized to 1-2-5 ladder (e.g. $0.10, $0.12, $0.15)
Risk ordering
Closest-to-spot = highest APR + highest assignment probability
3.4  APR Calculation
APR = ( user_premium / notional_usd ) * ( 365 / days_to_expiry ) * 100
user_premium = Black-Scholes fair_value * (1 - margin)
notional_usd = collateral_amount * spot_price
days_to_expiry = T * 365
04
Dynamic APR Engine
src/lib/aprEngine.ts
 computes a real-time margin deducted from the Black-Scholes fair
value before quoting the upfront premium. Four independent components are summed and
clamped to keep the protocol competitive while managing vault risk.
4.1  Utilization Margin — Kinked Curve
utilization = utilized_xlm / vault_cap_xlm
// Aave-style two-slope model
if utilization <
= KINK (0.80):
util_margin = BASE_RATE + utilization * SLOPE1
else:
util_margin = BASE_RATE + KINK * SLOPE1
+ (utilization - KINK) * SLOPE2 // steep above kink

Lusty Finance — Technical Architecture
Page 5
4.2  Inventory Penalty
Penalizes new deposits that worsen the vault's net delta or vega exposure. A covered call
deposit that deepens an already net-short-call book is penalized more than a put deposit
that partially offsets it.
4.3  Concentration Penalty — HHI
HHI = sum( (bucket_util_i / total_util)^2 ) for each strike bucket
// Activates when any single strike absorbs > 25% of total utilization.
// Prevents over-concentration of risk at one strike.
4.4  Flow Momentum Dampener
flow_ema = alpha * current_net_flow + (1 - alpha) * prev_ema
// Rapid inflow -
> lower offered APR (reduce risk accumulation speed)
// Rapid outflow -
> higher offered APR (incentivize new deposits)
4.5  Composite Formula
margin = util_margin + inventory + concentration + flow
margin = clamp(margin, 5%, 45%)
offered_apr = fair_apr * (1 - margin)
offered_apr = clamp(offered_apr, 2%, 500%)
05
API Layer
All financial logic runs server-side in Next.js Route Handlers. Authentication is implicit
— transaction signatures from the Stellar wallet prove ownership. No separate API keys are
issued to users.
Endpoint
Method
Description
/api/vault/deposit
POST
Verify payment tx on Horizon -> compute premium -> disburse
LUSD upfront
/api/vault/claim
POST
Settle expired position: spot-vs-strike -> return collateral
or assign
/api/vault/stats
Vault utilization: total cap, utilized XLM, open interest
/api/swap
POST
Verify payment -> execute Stellar path-payment -> send
destination asset
/api/leaderboard
Ranked users by points (deposit x1 + premium x3 + swap x0.5)
/api/faucet/lusd
POST
Rate-limited testnet LUSD distribution from issuer account
/api/research/commentary
Hourly AI desk notes (Gemini 2.0 Flash)
/api/admin/stats
Wallet-signature-gated admin metrics

Lusty Finance — Technical Architecture
Page 6
06
Data Layer
Persistent state is split between PostgreSQL (server-side, canonical) and browser
localStorage (client-side position cache). The database is provisioned on Supabase with
connection pooling managed by 
src/lib/db.ts
.
6.1  PostgreSQL Schema
users
wallet_address, first_seen, last_seen
transactions
id, wallet, action (deposit|claim|swap|faucet),
amount, asset, tx_hash, strike, expiry, option_type,
premium_paid, created_at
desk_notes
id, content, generated_at -- hourly AI commentary
admin_users
wallet_address -- allowlist for /api/admin/*
leaderboard_view (aggregated)
deposit_usd, upfront_lusd, swap_volume_usd,
points = deposit x 1 + upfront x 3 + swap_volume x 0.5
6.2  Client-Side Position State
src/lib/positions.ts
 serializes open positions to browser localStorage. Each record
stores: option type, strike, expiry, collateral amount, upfront premium received, and the
on-chain transaction hash. A production deployment would index positions server-side from
Horizon history.
07
Vault Caps & Risk Controls
Total XLM vault cap
1,000,000 XLM above baseline — bounds maximum net-short call exposure
Per-user 30-day limit
$50,000 notional USD — prevents single-wallet dominance
Per-strike 14-day bucket
$30,000 notional USD — limits concentration on any one strike
Price feed fail-safe
Deposits rejected if Binance spot is unavailable — no stale pricing
Rate limiting
Sliding-window limiter per (endpoint, wallet). Faucet: 3/hr, Deposits:
10/hr, Swaps: 10/hr
Tx verification
Premium disbursed only after Horizon confirms payment hash, amount,
asset, and sender

Lusty Finance — Technical Architecture
Page 7
08
Swap Facility
src/lib/swap.ts
 wraps the Stellar Classic DEX path-payment primitive to provide XLM <
-
>
LUSD conversion at approximately 0.1% spread. Quotes are derived from Binance spot; the
server verifies the inbound payment before executing the outbound path-payment from the
distributor account.
-
> returns { quote: '12.34', fee: '0.01' }
POST /api/swap { tx_hash, from_asset, to_asset, amount }
-
> verify inbound payment on Horizon
-
> execute path_payment_strict_send from distributor
-
> return { settlement_tx_hash }
09
Security Model
Admin auth
Wallet-signature challenge-response (src/lib/admin-auth.ts). Server
issues a nonce; wallet signs it; server verifies Ed25519 signature
against the allowlisted address. No passwords stored.
Input validation
Stellar address format validated on all endpoints. All SQL queries fully
parameterized — no string interpolation.
HTTP headers
CSP, HSTS, X-Frame-Options, Referrer-Policy, Permissions-Policy
configured in next.config.js.
Secrets
Distributor keypair, LUSD issuer keypair, database URL, and Gemini key
stored in .env.local (gitignored). Never sent to client.
Rate limiting
In-memory sliding-window (src/lib/rate-limit.ts) per endpoint + address.
Tx verification
Amount, asset, memo, and sender all confirmed on Horizon before payout.
10
Frontend Architecture
10.1  Page Structure (Next.js App Router)
src/app/
(app)/ -- marketing layout group (nav + footer)
earn/[asset]/ -- strike picker, premium quote, deposit button
(app)/dashboard/ -- open positions, claim expired options
swap/ -- XLM <
-
> LUSD swap interface
leaderboard/ -- Season 0 rankings
research/ -- TradingView chart + AI desk notes + news
docs/ -- protocol documentation
api/ -- all route handlers (financial backend)

Lusty Finance — Technical Architecture
Page 8
10.2  Key React Hooks
useXlmPrice
Streams XLM/USD from Binance WebSocket — drives real-time price display
useVaultStats
Polls /api/vault/stats every 30 s — drives utilization indicator
useWallet
Wraps Stellar Wallets Kit — exposes connect, disconnect, signTransaction
11
Repository Layout
Lusty/
next.config.js -- security headers, CORS, CSP
tailwind.config.ts -- design tokens (colors, fonts)
src/
app/ -- Next.js pages + API routes
components/
earn/ -- StrikeSelector, PositionSummary, EarnButton
dashboard/ -- PositionCard, ClaimButton
shared/ -- TokenInput, WalletButton, Modal
layout/ -- Navbar, Footer
lib/
pricing.ts -- Black-Scholes, vol smile, strike grid
aprEngine.ts -- Dynamic margin and APR engine
vault.ts -- Stellar payment transaction builders
swap.ts -- DEX path-payment builders + quote logic
db.ts -- PostgreSQL connection pool
db-queries.ts -- Leaderboard queries, transaction logging
positions.ts -- localStorage position persistence
rate-limit.ts -- Sliding-window rate limiter
admin-auth.ts -- Wallet-signature challenge-response
expiries.ts -- Dynamic expiry date generation
stellar.ts -- Horizon / Soroban endpoint config
hooks/ -- useXlmPrice, useVaultStats, useWallet
providers/ -- WalletProvider (Stellar Wallets Kit)
scripts/
mint-lusd.mjs -- Bootstrap LUSD issuer account
seed-lusd-offers.mjs -- Pre-populate DEX liquidity
public/ -- Static assets (token icons, hero images)

Lusty Finance — Technical Architecture
Page 9
12
Deployment & Network
Live URL
https://lusty.finance
Network
Stellar Testnet
Horizon endpoint
https://horizon-testnet.stellar.org
LUSD issuer
GBCMRD6NDL2RAJUOFQ25EHZVO3IRIGNESWE4QDRFB4AVFIP7IT5BRCJ6
Distributor account
GBAIN6CHZJGBL365JNXSRQEKALXYTWKXANQZ3RBM7AGUEYYKLJJ6SNR6
Database
PostgreSQL — Supabase managed, connection pooled
Backend runtime
Node.js — Next.js Route Handlers
Position state
Server: PostgreSQL (canonical) | Client: localStorage (cache)
Smart contracts
None — pure Stellar Classic payment operations