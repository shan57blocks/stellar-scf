Source: https://communityfund.stellar.org/submissions/rec5Y2MKyAzTJ6flK

# ROZO — SCF #38: Visa layer for stablecoins (Awarded, $150K)

### Products & Services

ROZO is the Visa layer for stablecoins. Our product is divided into two parts:

### 1. QR-Code One-Tap Checkout

-   **How Stellar Is Used:** Acts as our settlement rail for USDC payments—regardless of originating chain, funds route via Stellar’s fast, low-fee network.
    
-   **Impact:** Enables truly chain-agnostic, one-tap in-person payments: users simply scan a QR code, and settlement finalizes in seconds.
    

### 2. Cross-Chain Stablecoin Abstraction API

-   **How Stellar Is Used:** Powers our cross-chain routing layer alongside CCTP, automatically bridging any stablecoin into USDC on Stellar before payout.
    
-   **Impact:** Unlike CCTP’s multi-minute settlement, we achieve **sub-second finality**, making Stellar USDC truly viable for real-life consumer payments.


### Soroban

Yes

### Requested Budget



### Success Criteria

Output: Based on this product, combined with traffic partnerships, marketing, and brand promotion, we aim to reach 100K new users and 1M transactions within six months of launch.

Impact: We aim to deliver a scalable, production-ready version for a major stablecoin before Meridian, with our Stellar launch of this novel stablecoin-abstraction payment product. Position Stellar as the infrastructure layer powering real-world commercial consumer payments.


### Go-To-Market Plan

-   **Acquire Users via Wallet & Ecosystem Branding:**
    
    -   Social media and community promotions
        
    -   Product launch at in-person events like Meridian
        
    -   Targeted traffic campaigns
        
-   **Merchant-Driven User Acquisition:**
    
    -   **Offline Merchants** (e.g., restaurants, cafés): first-order discounts and subsidies to convert customers
        
    -   **Online Merchants** (e.g., AI service providers): cashback and bundled offers to attract users
        
-   **Brand Marketing:**
    
    -   Create video and written content showcasing real-world use cases
        
    -   Publish campaigns in top media, like Forbes, CoinDesk, and other media outlets
        
-   **Network Effects:**
    
    -   After seeding initial users, sustain growth with referral and invitation rewards


### Traction Evidence

We launched our MVP in April to validate user and merchant demand:

-   **Onboarded 8 merchants in one month** with an 80% conversion rate
    
-   **65% consumer adoption**: over 80 out of 120 community members used the product
    
-   **Bay Area partnerships & leads**:
    
    -   **F&B merchants**: 5 locations (Draper U & downtown SF)—a Udon shop, a café, and a burger joint—all charging a 3% credit-card surcharge
        
    -   **AI service providers**:
        
        -   **Committed to integrate**: Fellou (raised $30 M) and Creatify (raised $15 M)
            
        -   **Expressed interest**: GPU-rental platforms—plan to onboard 10+ AI service merchants in the Bay Area post-launch
            
-   **Generated 195 organic consumer waitlist sign-ups**
    
-   **Caught real-world attention**: Received positive feedback from Vitalik, Sandeep (Polygon), and Nick/Jessi (Base):
    
    -   <https://x.com/ROZOai/status/1930614859567370467>
        
    -   <https://x.com/ShawnMuggle/status/1925935348053369192>

### Tranche 1 (Deliverable Roadmap) - MVP

**Deliverable: Soroban Pay-In & Refund + EVM Settlement**

-   **Brief description:**
    
    We use Soroban on Stellar and an EVM settlement path to let users **pay in USDC on Stellar or Base** and have merchants **settle on the other chain** in seconds—without relying on a single custodian.
    
-   **How to measure completion:**
    
    -   instant settlement **<5s** (prefunded)
        
    -   20 mins liquidity settlement circle,
        
    -   End-to-end demo showing USDC pay-in on Soroban → instant settlement on Base
        
-   **Estimated date of completion:** 2025-08-31
    
-   **Budget:** $50 000
    
    -   Engineering: $40,000
        
    -   Infrastructure/Ops (CI, RPC, staging, monitoring bootstrap): $10,000


### Tranche 2 (Deliverable Roadmap) - Testnet

**Deliverable: Bi-directional Stellar ↔ Base Testnet**

-   **Brief description:**
    
    Deploy and configure cross-chain engine on Stellar Testnet and Base Sepolia to support transfers in both directions, with automated CCTP rebalancing and basic observability.
    
-   **How to measure completion:**
    
    -   Published testnet contract addresses & ABIs
        
    -   ≥ 300 successful Stellar→Base and Base→Stellar test transfers with ≥ 99% success
        
    -   Monitoring dashboard tracking latency (P95 < 10 s) and pool balances
        
-   **Estimated date of completion:** 2025-10-31
    
-   **Budget:** $50 000
    
    -   Engineering: $45,000
        
    -   Infrastructure/Ops (testnets, log/metrics pipelines, CI scale-up): $5,000


### Tranche 3 (Deliverable Roadmap) - Mainnet

**Deliverable: Stellar Liquidity Pool/Escrow + SDK 1.0 for Wallets/Merchants**

-   **Brief description:**
    
    Launch a Soroban-based liquidity pool/escrow on Stellar Mainnet to support $50M monthly volume, and publish SDK 1.0 for easy integration into wallets.
    
-   **How to measure completion:**
    
    -   Mainnet pool contract address published
        
    -   Protocol supports **1M+ monthly transactions**
        
    -   Published contracts, docs
        
    -   SDK 1.0 released on NPM/GitHub with sample integrations with wallets or Rozo App
        
-   **Estimated date of completion:** 2026-01-31
    
-   **Budget:** $50 000
    
    -   Engineering: $45,000
        
    -   Infrastructure/Ops (prod RPC, alerting, indexers, dashboards, SRE runbooks): $5,000


### Team

_We have a solid technical and financial academic background and rich experience in crypto payment._

Shawn writes the code – studied CS in Stanford, founded multiple companies (Redshift, Chainsights, MugglePay). He researched at Virtual Human interaction lab at Stanford. You can check out the codebase here: <<https://github.com/RozoAI/rozo-tap-to-pay>>  
LinkedIn: <https://www.linkedin.com/in/shawn-yu-37498b40/>

Sky co-founded MugglePay with Shawn and leads operations at Rozo. She worked at American Express. She brings deep crypto experience from her time as a blockchain researcher at the Tron Foundation. Sky **co-founded** MugglePay with Shawn in 2019, **helping** over 3,000 merchants accept crypto. MugglePay has processed $50M+ per month on blockchain.  
LinkedIn: <https://www.linkedin.com/in/sky-h-88811b118/>

### Links in the text

- https://x.com/ROZOai/status/1930614859567370467
- https://x.com/ShawnMuggle/status/1925935348053369192
