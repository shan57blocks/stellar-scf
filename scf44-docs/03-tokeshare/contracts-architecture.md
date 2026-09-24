Source: https://github.com/bim-finance-org/tokeshare-stellar-contracts/blob/master/docs/ARCHITECTURE.md

# Architecture

Two contracts, deliberately separate.

```
          buys / sells                     holds inventory
investor ───────────────► sale contract ──────────────────► RWA token
    │                          │                                 ▲
    │  USDC in / out           │ 5% fee on buybacks              │
    │                          ▼                                 │
    └──────────────────────► treasury                       admin controls
                                                    allowlist · blocklist
                                                    pause · freeze · cap
```

The token owns **ownership and compliance**. The sale owns **commerce** — price,
fees, buyback. Splitting them means the sale can be replaced (new price model,
new fee, a bug fix) without touching the token, its holders, or their balances.
That is not theoretical: the TFW_001 sale was already replaced once, going from
v1 to v2, while the token and every share stayed put.

---

## The RWA token — `contracts/rwa_token/`

A SEP-41 fungible token composing the plain OpenZeppelin extensions. There is
deliberately **no identity/KYC module**: the allowlist is a plain admin-managed
on-chain list, and how an address qualifies for it is decided off-chain.

| Capability | Source | Notes |
|---|---|---|
| transfer / approve / balance | `fungible::Base` + `FungibleToken` | SEP-41 |
| burn | `fungible::burnable::FungibleBurnable` | SEP-41 mandatory |
| max supply | `fungible::capped` | `cap = total_shares × 10^decimals` |
| allowlist | `fungible::allowlist::AllowList` | the contract's `ContractType` |
| blocklist | `fungible::blocklist::BlockList` | storage layer only — see below |
| pause | `stellar-contract-utils::pausable` | halts every balance movement |
| freeze (full + partial) | `rwa::RWA` freeze helpers | cherry-picked, no dispatcher |
| roles | `stellar-access::access_control` | `admin` + `minter` |

### Why the blocklist is composed by hand

`FungibleAllowList` requires `FungibleToken<ContractType = AllowList>` and
`FungibleBlockList` requires `ContractType = BlockList`. A contract has exactly
one `ContractType`, so the two traits are mutually exclusive.

`AllowList` is the contract type, so its audited overrides run on every base
path. The blocklist is reached through its storage layer (`BlockList::blocked`,
`block_user`, `unblock_user`) from gates this contract adds in its own overrides
of `transfer`, `transfer_from`, `approve`, `burn`, `burn_from` and `mint`.

### Why freeze does not need the RWA dispatcher

`RWA::set_address_frozen`, `freeze_partial_tokens` and `unfreeze_partial_tokens`
touch nothing but the frozen-amount storage keys and `Base::balance`. They pull
in neither the compliance dispatcher nor the identity registry, so they are
usable standalone — which is what makes a compliance token possible here without
a KYC module.

### The gates, in order

Every balance-moving entry point runs:

1. **not paused** — else `EnforcedPause` (1000)
2. **allowlisted** — else `UserNotAllowed` (113), sender *and* recipient
3. **not blocked** — else `UserBlocked` (114), sender *and* recipient
4. **not frozen** — else `AddressFrozen` (302), sender *and* recipient
5. **enough unfrozen balance** — else `InsufficientFreeTokens` (303)
6. base checks — `InsufficientBalance` (100), `ExceededCap` (106) on mint

Two details that are easy to get wrong:

- **The allowlist is re-checked in the contract's own gate**, not left entirely
  to `AllowList`, because `Base::mint` does **not** go through the
  `ContractType` overrides. Without it, the minter could issue shares to an
  address not allowed to hold them.
- **The frozen-balance check only fires when something is actually frozen.** A
  plain overdraft therefore still surfaces as `InsufficientBalance` (100) rather
  than being mislabelled as a freeze.

### Roles

- `admin` — allow / disallow / block / unblock / pause / unpause / freeze /
  unfreeze, and (as the access-control admin) granting and revoking roles, so
  the minter or the admin itself can be rotated without a redeploy.
- `minter` — `mint`, still bounded by the cap.

Privileged calls take an explicit `operator` (or `caller`) argument: the
`stellar-access` macros check that this address holds the role **and** require
its authorization. Non-authorized calls fail with `Unauthorized` (2000).

---

## The sale contract — `contracts/sale/`

Fixed price, no oracle, admin-updatable.

### Money flow

| Operation | Payment asset | Shares |
|---|---|---|
| `buy` | buyer → **sale contract** (100%) | sale → buyer |
| `sell` | sale → seller (net)<br>sale → **treasury** (fee) | seller → sale |
| `withdraw_pay` | sale → wherever the admin says | — |

Proceeds stay in the contract rather than going straight to the treasury: that
balance **is** the buyback float. The treasury only receives fees automatically.

This makes the liquidity policy explicit rather than implicit —
`withdraw_pay` is the lever. Take everything out and the buyback window is dry;
leave it in and it stays open. `buyback_available()` reports the real state so a
front end never invites a transaction that cannot succeed.

### Buyback

`sell` is the mirror of `buy`. Shares return to inventory and become sellable
again. `buyback_price = 0` closes the window. The fee applies to buybacks only,
in basis points, capped at 2000 (20%) as a guard against a fat-fingered
`set_fee_bps` confiscating a seller's proceeds.

The whole gross amount is checked before any transfer: paying the seller and
then failing on the fee would leave the books inconsistent.

### Rounding

Always in the contract's favour, or a sub-stroop drift becomes a drain:

- `buy` — cost rounded **up**
- `sell` — gross proceeds rounded **down**, fee rounded **up**

### Automatic allowlisting

When `auto_allow` is on, `buy` calls `allow_user` on the token before delivering
shares, with the sale contract itself as operator. This requires the sale to
hold the token's `admin` role (`grant_role`). The compliance machinery still
exists — blocklist, pause, freeze all still apply — but an ordinary buyer is not
stopped at the door.

It is a flag, not a default, because a plain SAC has no allowlist to call:
`auto_allow false` keeps the contract usable with any classic asset.

---

## Conventions

**7 decimals**, the Stellar convention. A "share" is one whole unit = `10⁷` base
units, so an asset split into `n` shares has `cap = n × 10⁷`. The sale's `SCALE`
assumes the same.

**Custom Soroban token, not a SAC.** `token::TokenClient` works against a custom
contract id directly, so no SAC wrapping is needed. Only the payment asset
(USDC) stays classic. Consequence: balances are **not** visible through Horizon
trustlines and must be read from the token contract.

**Instance TTL.** Metadata, cap and config live in instance storage, and every
state-changing call extends the instance by 30 days. A contract left completely
idle long enough can still have its entries archived and need a paid
restoration — worth a periodic call on a long-lived mainnet asset.

**Neither contract is upgradeable.** For the token that is deliberate: a holder
gets a guarantee that the rules cannot change under them. Everything that
legitimately needs to change — pause, freeze, blocklist, price, fee, roles — is
already adjustable without a redeploy. The sale is cheap to replace when the
commercial logic evolves.

---

## Error codes

Sale codes are 1–7 and token codes are 100+, so a front end can tell them apart
without ambiguity.

| Code | Contract | Meaning |
|---|---|---|
| 1 | sale | invalid price |
| 2 | sale | invalid amount |
| 3 | sale | not enough inventory |
| 4 | sale | not initialized |
| 5 | sale | buyback window closed |
| 6 | sale | buyback underfunded |
| 7 | sale | fee above the 20% cap |
| 100 | token | insufficient balance |
| 106 | token | mint would exceed the cap |
| 113 | token | address not allowlisted |
| 114 | token | address blocked |
| 302 | token | address frozen |
| 303 | token | not enough unfrozen balance |
| 1000 | token | contract paused |
| 2000 | token | caller lacks the required role |

---

## Tests

50 tests, run with `cargo test`.

**Token (29)** — mint up to cap and rejection beyond; allowlisted transfers, and
rejection to/from non-allowlisted; blocklist; burn reducing balance and supply;
pause halting then resuming transfers; full and partial freeze; role enforcement
on every privileged call; and SEP-41 interop, verifying the generic
`token::TokenClient` can move the token — the sale depends on it, and a mismatch
there would break every purchase.

**Sale (21)** — payment retained in the contract; buyback paying net of fee;
buyback closed and underfunded paths; float accounting; fee configuration and
cap; rounding in the contract's favour; admin withdrawals. The automatic
allowlisting is exercised against a mock token that refuses non-allowlisted
recipients, since it is the one cross-contract call in the system.
