Source: https://github.com/bim-finance-org/tokeshare-stellar-contracts/blob/master/README.md

# Tokeshare — Stellar RWA contracts

Soroban infrastructure for tokenizing real-world assets on Stellar: a reusable
**SEP-41 compliance token**, a **fixed-price sale contract with buyback**, and
the deployment pipeline that issues one token per asset.

Each tokenized asset ("bien") gets its own token contract, its own cap, its own
allowlist, and its own sale. The [tokeshare](https://tokeshare.co) app consumes
the deployed contract ids — pasted by hand into a config file, no database and
no runtime coupling.

**New here? Start with [docs/ADDING-AN-ASSET.md](docs/ADDING-AN-ASSET.md)** — it
walks through tokenizing an asset end to end, from an empty params file to a
buyable share.

---

## Contents

| Path | What |
|---|---|
| [`contracts/rwa_token/`](contracts/rwa_token) | SEP-41 RWA token: allowlist, blocklist, cap, pause, freeze, burn |
| [`contracts/sale/`](contracts/sale) | Fixed-price sale + buyback with a fee |
| [`contracts/distributor/`](contracts/distributor) | Revenue distributor: Merkle-verified USDC payouts, push + claim |
| [`scripts/`](scripts) | Deploy / allowlist / lifecycle pipeline (bash + stellar CLI) |
| [`docs/ADDING-AN-ASSET.md`](docs/ADDING-AN-ASSET.md) | **How to tokenize a new asset**, step by step |
| [`docs/ARCHITECTURE.md`](docs/ARCHITECTURE.md) | How the contracts are built and why |
| [`docs/OPERATIONS.md`](docs/OPERATIONS.md) | Running a live asset: compliance, pricing, buyback, funds |

## Quick start

```bash
cargo test              # 73 tests (29 token + 21 sale + 23 distributor)
stellar contract build  # -> target/wasm32v1-none/release/*.wasm
```

Requires Rust with the `wasm32v1-none` target and
[stellar-cli](https://developers.stellar.org/docs/tools/developer-tools/cli/stellar-cli)
26+.

To deploy, see [docs/ADDING-AN-ASSET.md](docs/ADDING-AN-ASSET.md).

## What the token does

A share is a whole unit of a SEP-41 token with **7 decimals**, so an asset split
into `n` shares has `cap = n × 10⁷` base units. Beyond the standard transfer /
approve / balance / burn, every balance movement passes five gates:

| Gate | Error | Meaning |
|---|---|---|
| not paused | `#1000` | the issuer has halted all transfers |
| allowlisted | `#113` | sender **and** recipient must be on the list |
| not blocked | `#114` | either party has been banned |
| not frozen | `#302` | either party's whole balance is frozen |
| enough unfrozen balance | `#303` | part of the sender's balance is frozen |

Supply is bounded by the cap; burning frees room under it again.

Roles: **admin** (allow / block / pause / freeze, and role management) and
**minter** (mint, up to the cap).

## What the sale does

The sale holds inventory and sells it at a fixed price against a payment asset
(USDC). Proceeds **stay in the sale contract** — that balance is what funds the
buyback. `sell` is the mirror of `buy`: shares return to inventory, the seller is
paid out of the float minus a fee that goes to the treasury.

The buyback is only as open as the contract is funded. `buyback_available()`
reports how many shares can be bought back right now, so a front end can show
the real state instead of letting someone sign a transaction that cannot
succeed. There is **no secondary market** — no order book, no peer listing.

The sale also allowlists buyers automatically (when `auto_allow` is on and it
holds the token's admin role), so compliance machinery exists without blocking
ordinary purchases.

## What the distributor does

One global contract distributes each asset's monthly USDC revenue to its
holders. A Soroban token has no on-chain holder registry, so the payout list is
computed off-chain (an indexer maintains the holder set; every balance is
re-read from the token contract) and anchored on-chain as a **Merkle root** —
32 bytes per cycle, whatever the holder count.

`create_cycle` deposits the cycle's USDC and publishes the root. Payouts then
follow two paths, both verified against the root — the contract never trusts
the server that computed the list:

- **push** (nominal): the operator calls `distribute_for` with the payout
  lines and their proofs — every holder with a USDC trustline is paid in one
  go, and already-paid lines are skipped so batches can be retried safely;
- **claim** (catch-up): a holder the push had to skip (no USDC trustline at
  the time) claims their line themself once their account can receive USDC.

A solvency guard caps a cycle's cumulative payouts at its deposit, so a
miscomputed root can never draw on other cycles' funds. After `expires_at`,
`sweep` recovers whatever was never paid and closes the cycle. The typed
events (`cycle_created`, `paid`, `swept`) are the indexer's interface;
`paid.pushed` distinguishes operator pushes from holder claims.

The Merkle encoding is mirrored bit-for-bit by the TypeScript builder in the
tokeshare app; both sides pin the same fixture root in their test suites, so
either implementation drifting breaks its own tests
([`contracts/distributor/src/merkle.rs`](contracts/distributor/src/merkle.rs)).

Deploy with [`scripts/deploy-distributor.sh <network>`](scripts/deploy-distributor.sh)
— one instance per network, serving every asset.

## Deployed instances

### TFW_001 — mainnet

A quad (transport vehicle) tokenized into 100 shares at 50 USDC.

| | |
|---|---|
| Token | [`CC2MTI3REEQUMDJYOLQHK4D6GPGSTGPZOWH3G75SECSOTOH7L7GGLG5O`](https://stellar.expert/explorer/public/contract/CC2MTI3REEQUMDJYOLQHK4D6GPGSTGPZOWH3G75SECSOTOH7L7GGLG5O) |
| Sale (v2) | [`CAFYUWC2U7GX4DNG6JRZLPYDNXDOLJNHP5RWVAMOTOA5QO75N4B56X45`](https://stellar.expert/explorer/public/contract/CAFYUWC2U7GX4DNG6JRZLPYDNXDOLJNHP5RWVAMOTOA5QO75N4B56X45) |
| Sale (v1, retired) | `CDF7YM4PHBXCKCSCIRFZM42ZYPDGC5QCUA4DRUFDPVSHIQILXHE7OS6C` |
| Payment asset | USDC (Circle) SAC `CCW67TSZV3SSS2HXMBQ5JFGCKJNXKZM7UQUWUZPUTHXSTZLEO7SJMI75` |
| Admin / minter / distributor / treasury | `GCBHFIQNNXYC6AVKCFAC3I6EN3E2ZDNBIQ6RUSZDS2IMYVRXAWZH3OYZ` |
| Price | 50 USDC / share · buyback 50 USDC, 5% fee |
| Token wasm hash | `191a9dcfc76dd609c1e086103756aa79abf9b1ebc02744d4fcb4cc3aa26eb574` |
| Sale wasm hash | `1cfa6745881012285b11bbc9f246c4752d4db22ae3fcf12c488fef625b993124` |

### TRES — testnet

Real-estate asset "Angel Cœur Caribe", 23 000 shares. A SEP-41 re-issue of the
existing classic TRES asset, keeping the token count unchanged (22 996.8 held in
its SAC + 3.2 on trustlines = 23 000, per Horizon `/assets`). The classic asset
is untouched.

| | |
|---|---|
| Token | `CCZERUHBBFK2TIMOGYMTROUMKPLTMFQ3KRFDASGKV3IZCAZHBFEI6J6Q` |
| Sale (v1) | `CCCDPOYCHMAVCISAQVHCNKJNIMWFISOGQWLAMQAEFOIZW76EGP77CJHF` |
| Payment asset | test USDC SAC `CAW2SVC7HTEFP64JVQSHIZNOYCOKPE54IPCSAD3AKG2ZYMUWQFQB7KVH` (issuer `GCYG5OOZY4O2EZOY7OPT4FYY2XWZQ3WCX6M24CVWWHTV67ATKAVK77QC`) |
| Price | 10 USDC / share |

> The TRES sale still runs the **v1** contract: no buyback, no fee, no automatic
> allowlisting. Redeploying it with v2 on testnet costs nothing but friendbot
> XLM — see [docs/ADDING-AN-ASSET.md](docs/ADDING-AN-ASSET.md).

## What it costs

Fees are dominated by **storage rent**, which is proportional to the wasm size
and roughly 4× more expensive on mainnet than on testnet. Measured, not
estimated:

| Operation | testnet | mainnet |
|---|---|---|
| Upload the token wasm (37 453 B) | ~6 XLM | **26.78 XLM** |
| Deploy a token instance + allowlist + mint | ~0.8 XLM | ~0.30 XLM |
| Upload the sale wasm (11 721 B) | — | **14.98 XLM** |
| Deploy a sale + allowlist + fund inventory | ~0.3 XLM | ~0.12 XLM |
| One allowlist / lifecycle call | ~0.01 XLM | ~0.03 XLM |

**The upload is paid once per wasm, not per asset.** A second asset reuses the
same hash and pays only for its instance — a fraction of an XLM. Budget the big
number for the first mainnet deployment only.

`deploy-token.sh` simulates the upload fee against the target network before
touching anything, and refuses to start if the balance cannot cover it.

## Design decisions worth knowing

**Custom Soroban token, not a classic-asset SAC.** The sale reaches it through
`token::TokenClient`, which works against a custom contract id directly. Only
USDC stays a classic asset wrapped in a SAC. One consequence for front ends:
balances of a custom token are **not** visible through Horizon trustlines — read
them from the token contract.

**Allowlist and blocklist cannot both be the contract type.** `FungibleAllowList`
requires `FungibleToken<ContractType = AllowList>` and `FungibleBlockList`
requires `ContractType = BlockList`. `AllowList` is the contract type; the
blocklist is composed through its storage layer. See
[docs/ARCHITECTURE.md](docs/ARCHITECTURE.md).

**Freeze without the RWA dispatcher.** The freeze helpers touch only the
frozen-amount storage keys, so they are usable without pulling in the identity
registry or compliance dispatcher — no KYC module here. How an address qualifies
for the allowlist is decided off-chain and is out of scope.

**Neither contract is upgradeable.** Changing a contract means deploying a new
one and migrating. For the token that is deliberate: holders get a guarantee the
rules will not change under them. The levers that matter — pause, freeze,
blocklist, price, roles — are all adjustable without a redeploy.

## Built on

- [soroban-sdk](https://docs.rs/soroban-sdk) 26
- [OpenZeppelin Stellar Contracts](https://github.com/OpenZeppelin/stellar-contracts)
  0.7.2 — `stellar-tokens`, `stellar-access`, `stellar-contract-utils`,
  `stellar-macros`

## License

MIT
