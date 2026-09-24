Source: https://app.notion.com/p/fuul-app/Stellar-Technical-Architecture-36ea045a4b2880769909d0e54fe99149

(Exported from the public Notion page. The 'Data Flow' diagram is an image and is not included.)

Stellar — Technical Architecture

### **Fuul on Stellar — Technical ****Architecture**

**Overview**
Fuul's Stellar integration extends its multi-chain incentive infrastructure natively onto Stellar through three layers: (1) platform-level support across Fuul's webapp, APIs, and SDK, (2) pre-built trigger connectors for ecosystem DeFi protocols, and (3) Soroban smart contracts for non-custodial reward distribution

---

### **Layer 1 — Platform Extension**

- **REST API:** Stellar added as a supported chain_id; program creation, event ingestion, allocation calculation, and distribution endpoints extended to handle Stellar addresses and asset formats
- **SDK:** JavaScript/TypeScript SDK updated to support Stellar transaction signing for claim flows; the API endpoint returns the claim data and the client application constructs and submits the transaction
- **Webapp:** no-code program configuration UI supports Stellar chain selection, trigger configuration, and reward token selection (XLM, stablecoins, or any SEP-41 Stellar token)
- **Analytics:** Stellar-specific metrics (active wallets, reward volume by token, protocol-level attribution) surfaced in existing analytics dashboards
---

### **Layer 2 — Protocol Trigger Integrations**

**Aquarius (DEX)**
Connector subscribes to Aquarius liquidity pool events on Stellar's ledger. Tracks: LP token minting/burning (liquidity provision), swap volume per wallet, and time-weighted position sizes. Enables projects to reward users for providing liquidity or reaching trading volume thresholds. 

Implementation: Connector indexes Aquarius' Soroban AMM contract events and pool state directly from the Stellar ledger, persists event state in Fuul's event store, and normalizes them to Fuul's unified event schema before ingestion into the incentive engine.

**Blend v2 (Lending)**
Connector reads Blend v2's index to track: supply positions, borrow positions, and utilization over time per wallet. Enables protocols to reward lenders and borrowers automatically. 

**Implementation**: Connector indexes each Blend pool's on-chain positions — supply (bToken) and borrow (dToken) balances per wallet — directly from the Stellar ledger, persists position state in Fuul's event store, and normalizes them to Fuul's unified event schema before ingestion into the incentive engine.

**Stellar Token Holders**
Connector reads holding positions for any specific token on the Stellar Network, allowing their issuers to reward users as they wish.

**Implementation**: Connector indexes each Blend pool's on-chain positions — supply (bToken) and borrow (dToken) balances per wallet — directly from the Stellar ledger, persists position state in Fuul's event store, and normalizes them to Fuul's unified event schema before ingestion into the incentive engine.

**Connector architecture**
Each connector is an independent indexer that maps a protocol's on-chain activity into Fuul's unified event schema. The incentive engine consumes that schema identically across every chain — so supporting a new protocol or network is just a new connector, with no changes to allocation or distribution logic.

---

### **Layer 3 — Soroban Reward Distribution Contracts**

- Reward allocation engine: Fuul's attribution engine produces signed claim checks — each a signed allocation (recipient, token, amount, reason, deadline) — that users redeem on-chain.
- Claim flow: users (or the project, on their behalf, for gasless delivery) submit their signed claim checks via Stellar Wallets Kit — through Fuul's hosted claiming page or any app integrating Fuul's APIs/SDKs (white-label). The contract verifies the signature(s) and releases the reward; funds are never custodied by Fuul.

- Auth & signers: each claim check is verified against a set of authorized signer roles with a configurable multi-signature threshold (mirrors FuulManager's CLAIM_SIGNER_ROLE + requiredSigners on EVM) — funds stay safe even if one signer key is compromised. Uses Soroban's native auth; no custodial keys.

- Contract interface:
deposit(token, amount) — project funds the reward budget(or transfers tokens directly to the contract)

claim(claim_checks) — submit one or more signed claim checks; contract verifies signatures and releases rewards

withdraw(admin_only) — project recovers unspent or expired budget

State: budget balance per token; authorized signer set + required-signature threshold; per-claim-check spent state to prevent double-claims.
---

### **Integration with Official SCF Building Blocks**

| Building Block | Integration |
|---|---|
| Aquarius | Trigger connector for LP and trading volume events |
| Blend v2 | Trigger connector for lending/borrowing positions |
| Stellar Token Holders | Trigger connector for any SEP-41 token holding positions |
| Stellar Wallets Kit | Transaction signing in claiming pages and white-label integrations |

### **How Fuul’s Existing Infrastructure Maps to Stellar**

| Component | Status |
|---|---|
| Event indexing engine | Already handles EVM + Solana — extend with Horizon event stream |
| Allocation + incentive engine | Chain-agnostic, no changes needed |
| Distribution contracts | New work: Soroban contract |
| SDK signing flow | New work: Stellar transaction signing |
| Connector framework | Extend existing pattern — new connectors for Aquarius, Blend v2, Token Holders |

## Data Flow

[image: attachment:57320cf3-98c4-420a-8243-5c2c9d3270b4:2f71d32d-b7c4-466f-9bc3-f4a9eece6420.png]

