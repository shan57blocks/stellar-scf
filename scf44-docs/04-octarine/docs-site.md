Source: https://docs.octarine.finance (pages: /overview/introduction, /overview/how-it-works, /contracts/addresses)

---
Page: https://docs.octarine.finance/overview/introduction


# Introduction

## What Is Octarine

Octarine is an RFQ DEX which gathers liquidity via trading firm bids to find the best possible path to give instant liquidity to any supported asset, via a transparent and competitive auction mechanism. This can be used for a) instant redemptions or b) fallbacks for lending market liquidations.

The API assumes trading firms advance the liquidity to fill the order and receive the corresponding collateral plus a premium for doing so. It is modeled after the[ 0x Protocol Orderbook](https://docs.0xprotocol.org/en/latest/basics/orders.html#the-orderbook) and[ RFQ Orders](https://docs.0xprotocol.org/en/latest/basics/orders.html#rfq-orders), and adapted for the intended use cases. Settlement can be either atomic or non-atomic, making sure capital only moves when a trade is confirmed.

A couple of things to note:

* The API has a **smart routing** feature, which enables it to settle a position across multiple bids (e.g. 20% on an AMM and 80% with the best trading firm quote).
* The API also has a **fallback feature** - if the winning bid backs out, the trade will go to the second-best bid and so on.

---
Page: https://docs.octarine.finance/overview/how-it-works


# How It Works

## Where do Trades Come From

Trades are originated both directly via the Octarine UI and via integrated partner venues utilizing Octarine for RWA liquidity. We are connecting to several parties to generate flow that you can bid on. Now, let's look at how the auction process happens on Octarine.

## Process Overview

There are 4 stages to the lifecycle of an auction on Octarine via the RFQ API, namely:

1. **Detection** - Octarine adds an instant redemption request or money market liquidation to its feed, for bidders to see;
2. **Order Submission** - bidders send Octarine their bids;
3. **Solver Selection** - Octarine assesses all bids and chooses the best one;
4. **Settlement** - We settle the trade with the winning bidder.

All of this happens in milliseconds, giving the users an instant UX. Let us now look at each step in more detail:

### 1. Detection

The protocol continuously monitors requests by instant redemption partners and the relevant money market accounts to be liquidated. In the case of liquidations, when an account’s Health Factor reaches < 1, it is flagged as liquidatable and added to our API feed. As for instant redemption requests, when a request is received, it is then echoed to Solvers via the Octarine API.

### 2. Order Submission

This is the auction process, in which the RFQ accepts bids during a period of time (e.g. 3 minutes). During this period, Solvers query the feed, evaluate available RFQ opportunities and respond with signed RFQ orders (via POST /octarine/bid). Orders use token approvals so Solvers don’t need to transfer tokens upfront, only at settlement.

### 3. Solver Selection

Octarine evaluates all quotes and selects the best order for each opportunity (read, best price). To note, multiple fills may occur per opportunity.

### 4. Settlement Flows

* **Liquidations Settlement Flow 1 - via flashloan (default)**

The settlement flow is different for an RWA instant redemption and for a lending market liquidation. Let's first look at the flow for liquidations for a Morpho market, which will happen atomically through a flashloan:

1. Octarine selects the best RFQ order(s) for the underwater account;
2. Via [Bundler](https://docs.morpho.org/learn/concepts/bundlers/), Octarine calls GeneralAdapter.morphoFlashloan() to borrow the amount needed to repay the outstanding debt;
3. Inside the flashloan callback, Morpho.liquidate(account, borrowedAsset, collateralAsset, amount) is called using the flash-borrowed funds, which releases the position's collateral;
4. Still inside the callback, Octarine calls the RFQ contract's 0xSettlement.fillRFQOrder(order, sig) with the winning Solver's bid and token permit, effectively giving them the trade and sending them the position's collateral in exchange for the position's debt asset (e.g. USDC);
5. Octarine repays flash loan with the assets received from Solver;
6. Fees are distributed to the Solver minus a protocol fee;
7. Solver redeems the collateral for their original asset (optional).

This flow enables signing orders, but depends on flashloans to work. This means it is capped by the liquidity available in the market. Better for small and medium-sized liquidations.

* **Liquidations settlement flow 2 - via smart wallet**

1. Octarine selects the best RFQ order(s) for the underwater account;
2. Winning bid gives required token approval to Octarine's smart account wallet;
3. Via the smart wallet, the below calls are bundled and called atomically:

* Octarine takes assets from Solver and sends them to the smart wallet;
* Octarine's smart wallet calls Morpho.liquidate(account, borrowedAsset, collateralAsset, amount) and repays the loan using the transferred funds, which releases the position's collateral to the smart wallet;
* Smart wallet returns collateral to Solver minus a protocol fee;

Dependency on flashloans is removed in this format, meaning the flow is uncapped. The signing of orders is, however, unavailable. Better for large liquidations.

* **Swap/instant redemptions settlement flow**

As for the case of an instant redemption, the flow is much simpler:

1. Octarine selects the best RFQ order(s) for instant redemption request;
2. Octarine calls the RFQ contract's 0xSettlement.fillRFQOrder(order, sig) with the winning Solver's bid and token permit, effectively giving them the trade and sending them the asset to be redeemed in exchange for their assets (e.g. USDC);
3. Fees are distributed to the Solver minus a protocol fee;
4. Solver redeems the redeemed asset for their original asset (optional).

---
Page: https://docs.octarine.finance/contracts/addresses


# Addresses

{% tabs %}
{% tab title="Ethereum Mainnet" %}

| Contract      | Address                                                                   |
| ------------- | ------------------------------------------------------------------------- |
| Proxy         | <https://etherscan.io/address/0xF30fFE4E387ee7B814fA0bb093d53dcC253C63Bc> |
| Native Orders | <https://etherscan.io/address/0xC70E55336C8D0f4625858f2dB4eA65dCE9E8b3c1> |
| Ownable       | <https://etherscan.io/address/0x07945DC9b578b6a3a6C27535fd5795B85B134cFc> |
| OTC Orders    | <https://etherscan.io/address/0x8244bA2751F07Bdb4f9f42f9244cE9bbC42b4937> |
| {% endtab %}  |                                                                           |

{% tab title="Arc Testnet" %}

| Contract      | Address                                                                          |
| ------------- | -------------------------------------------------------------------------------- |
| Proxy         | <https://testnet.arcscan.app/address/0xBDCF5dcd60F967C2f8c79AFD1CE7C9F1A11f9f04> |
| Native Orders | <https://testnet.arcscan.app/address/0x10da8Eb89525A12FeCC359fFB7698eBAb16b2b16> |
| Ownable       | <https://testnet.arcscan.app/address/0x27B59704C2AD666fee1C89708022f1778a853D55> |
| OTC Orders    | <https://testnet.arcscan.app/address/0x438b8E1b3Dd96FaF3755Fc5f90eB8b1F5b95a97F> |

{% endtab %}
{% endtabs %}
