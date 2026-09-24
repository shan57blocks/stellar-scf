Source: https://communityfund.stellar.org/submissions/recrCfz6tNAqdM3Ho

# ROZO — SCF #43: Pay for Claude & ChatGPT via Stellar (Not awarded, $150K requested)

### Products & Services

#### **ROZO: The Visa Layer for Crypto**  

ROZO serves as the "Visa Layer" for the crypto ecosystem. In traditional finance, Visa provides two core services to banks: **1) Multi-currency clearing and settlement**, and **2) Integration of a global merchant network** for bank customers to spend.

With the support of **SCF #38**, ROZO successfully developed the first pillar: a liquidity layer for multi-chain USDC and EURC anchored on Stellar.

In this round, we are focused on delivering the second pillar: **Merchant Network Integration.** Through permissionless technical integration, we enable users to purchase AI tokens (including models like **Claude, Gemini, and ChatGPT**) using natural language on Stellar.

**Furthermore, we are integrating this merchant network directly into Stellar wallets, including LOBSTR, Beans, Decaf, and Freighter. If wallets are the "Bank Layer" of crypto, ROZO is the "Visa Layer."**

**Check out our demo integrated into LOBSTR:** <https://youtube.com/shorts/bhRke7lmNH0?feature=share>  

- _ _

####   
**Core Features**  

##### **1. Intent-Based AI Checkout**

-   **Stellar’s Role:** Utilizes **Stellar USDC** for payment execution and **Memos/Claimable Balances** to map transactions to specific AI service invoices.
    
-   **Impact:** Converts complex provider URLs into verified **"one-tap"** actions, eliminating the need for users to manually enter addresses or amounts.
    

##### **2. Automated Settlement Layer**

-   **Stellar’s Role:** ROZO’s liquidity layer is already live on Stellar and integrated with projects like **LOBSTR and Defindex**, with **Beans and Blend** currently in the integration pipeline.
    
-   **Impact:** Eliminates waiting periods for cross-chain or off-chain fulfillment, making crypto payments as fast as traditional credit cards.
    

##### **3. Plug-and-Play Wallet Module**

-   **Stellar’s Role:** Leverages **SEP-7 (Delegated Signing)** and **SEP-30** compatibility to trigger seamless transaction signing within native Stellar wallets.
    
-   **Impact:** Provides wallets with immediate utility and high-demand spending use cases without requiring them to build custom merchant adapters.
    

##### **4. On-Chain Rewards & Cashback**

-   **Stellar’s Role:** Issues Stellar-native tokens (**Seed**) via **Soroban** or Asset Issuance to automate distribution and redemptions.
    
-   **Impact:** Drives user retention and ecosystem loyalty by embedding a transparent, programmable incentive loop directly into the checkout flow.  
    

- _ _

###   
**Differentiation from Existing Solutions**  

1.  **Permissionless vs. Cards:** Unlike traditional card networks, ROZO’s merchant network is **permissionless**. It requires no KYC, has no regional restrictions, and is accessible to the unbanked, empowering anyone to spend on the Stellar network.
    
1.  **Intent-Based vs. Complex Crypto Ops:** ROZO’s payment flow is driven by **natural language**. Users don’t need to understand blockchain technicalities—they simply express their intent. This significantly lowers the barrier to onboarding new, non-technical users to Stellar.
    

###   
**Funding Goal**  

This grant will enable us to integrate our first major merchant, **OpenRouter**, making over **100 AI models** (including Claude, Gemini, and ChatGPT) purchasable via **Stellar USDC**. This is the first step in bringing a permissionless merchant network to Stellar. Following this round, we will continue to scale by integrating a wider array of global merchants.

### Requested Budget



### Traction Evidence

**In Q1, ROZO served over 1,000 active users on Stellar and processed over $7M in transaction volume.** (The data before March 2, plus the data in March, the total transaction volume exceeds $7M <https://dune.com/rozointents/stellar>)

**Proof of Integration:** We have successfully integrated with **Defindex, Soroswap, LOBSTR, and Mykobo**, and are currently finalizing integrations with **Trustless Work, Beans, and Seevcash**.

Our track record demonstrates mutual growth: we have consistently acquired new users through our existing partners while driving significant traffic back to their platforms. Based on this proven synergy, we are confident in our ability to onboard more partners and continue fostering reciprocal growth across the Stellar ecosystem.

Previously, our discounted AI service offerings on **ROZO Rewards**—such as **Lovable**—consistently sold out within days. Based on this proven traction, we are confident that the **permissionless integration** of Stellar payments with AI services via **ROZO Intents** will scale our current volume by **10x**.

Upon completion of this integration, we aim to migrate **$70M in AI consumption** and the global community of **AI developers** behind it onto the Stellar network.

### Tranche 1 (Deliverable Roadmap) - MVP

**Brief Description:** On the ROZO platform, we enable **intent-based payments** powered by **Stellar USDC**. By analyzing **OpenRouter invoice URLs**, the system interprets user payment intent and executes the transaction seamlessly on the Stellar network. This milestone consists of the following 3 functions:

-   **Intent Extraction:** When users purchase tokens for AI models (such as **Claude, Gemini, or ChatGPT**) via OpenRouter, they can simply paste the payment invoice into the **ROZO natural language interface**. ROZO will automatically extract the payment intent, including the **destination address, payment amount, and memo** (if required).
    
-   **User Confirmation & Execution:** After identifying the intent, ROZO presents the extracted details back to the user via the chat interface for verification.
    
-   **Seamless Signing:** Upon user confirmation, the wallet signing process is triggered simultaneously. Once the **Stellar-USDC** payment is processed, the corresponding AI token credits (for Claude, Gemini, GPT, etc.) are credited to the user's account.
    

**How to Measure Completion:**

-   **Public Demo URL (Web Page):** A live web application will be provided for reviewers to verify the following core functionalities:
    
    1.  **Intent Extraction:** When a user sends an OpenRouter invoice in the natural language interface, ROZO must successfully extract and present the correct payment intent with **100% accuracy**.
        
    1.  **Seamless Execution:** The system must trigger a seamless wallet signing process upon user confirmation to finalize the payment.
        
-   **On-chain Verification:** A list of transaction hashes from a blockchain explorer will be provided as evidence of **10 completed on-chain payments**.
    
-   **Performance Benchmark:** Documentation of these transactions must demonstrate that **over 90%** of the payments achieved **sub-second confirmation** on the Stellar network.
    

**Budget:** $45,000(10%+20%)

### Tranche 2 (Deliverable Roadmap) - Testnet

**Brief Description:Wallet Integration:** Users can achieve a **"one-tap-to-pay"** experience within any Stellar-USDC-supported wallet to purchase token credits for AI models, including **Claude, Gemini, and GPT**. This milestone consists of the following 3 parts:

-   **Developer Suite:** Delivery of all necessary components for wallet integration, including robust **APIs and comprehensive technical documentation**.
    
-   **Seamless User Experience:** Post-integration, users will be able to purchase the aforementioned AI services using **natural language** directly within their preferred wallet.
    
-   **Optimized Checkout:** The integration must achieve a **"one-tap-to-pay"** experience that is both simpler and more secure than traditional credit card payments.
    

**How to Measure Completion:**

-   **Cross-Wallet Demo:** A public demo URL (Wallet/App version) will be provided to verify interoperability with at least one of the following wallets: **LOBSTR, Beans, Decaf, or Freighter**.
    
-   **In-Wallet Signing:** Reviewers can verify that the wallet-native signing process is triggered seamlessly upon intent confirmation to complete the payment.
    
-   **On-chain Evidence:** Transaction hash links for **10 completed on-chain payments**, demonstrating that **over 90%** of transactions achieve **sub-second confirmation**.
    
-   **Integration Specifications:** Delivery of detailed integration blueprints for the four target wallets (**LOBSTR, Beans, Decaf, Freighter**), ensuring immediate technical readiness for partnership onboarding.
    
-   **Technical Documentation:** A public link to the complete **Wallet Integration API Documentation**.
    

**Budget:** $45,000(30%)

### Tranche 3 (Deliverable Roadmap) - Mainnet

**Brief Description: Mainnet launch + On-chain Rewards:** Upon completion of a purchase, users earn **on-chain cashback** on the Stellar network. This cashback can be applied toward future AI model purchases, leveraging a distinct **price advantage** to acquire new users and incentivize long-term retention within the Stellar ecosystem. This milestone consists of the following 3 parts:

-   **Cashback Issuance:** Following a successful purchase, ROZO will notify the user of the earned cashback. This reward is issued as a **native Stellar token**, which can be viewed, transferred, or traded on-chain, or redeemed as a discount for subsequent AI model transactions.
    
-   **Cashback Redemption:** During the checkout process, users can instruct ROZO via natural language to use their **cashback tokens** instead of USDC. ROZO will calculate and display the required token amount for the transaction; once confirmed, the user can complete the purchase using the reward tokens.
    

**How to Measure Completion:**

-   **Public Demo (Web/Wallet/App version):** A live demo environment where reviewers can verify the **incentive loop**: after completing a payment via natural language interaction, users are automatically issued **ROZO $Seed** tokens (temporary token name). These tokens can then be successfully redeemed to purchase subsequent AI model credits.
    
-   **On-chain Contract Address:** Delivery of the **verified smart contract address** (Soroban/Stellar asset) for the cashback token, allowing independent audit of the issuance and redemption logic on the Stellar network.
    
-   **Mainnet Product Demo Video & Comprehensive Test Report:** A walkthrough video and a detailed technical report confirming that all features—including intent extraction, payment execution, and cashback fulfillment—are fully operational on the **Stellar Mainnet**.
    

**Post-Launch Volume Targets**

We aim to achieve a total **transaction volume of $70M** within the first year following our mainnet launch. This target is grounded in two key factors:

1.  **Proven Traction:** We have already successfully processed **$7M** in volume through our current phase.
    
1.  **Founder Track Record:** Our core team, Sky and Shawn, brings deep expertise from their previous venture, **MugglePay**, where they managed a peak monthly transaction volume of **$50M**.
    

Given this extensive experience in scaling payment systems and our current growth trajectory, we are confident that the **$70M annual volume target** is both realistic and attainable.

**Budget:** $60,000(40%)

### Team

_We have a solid technical and financial academic background and rich experience in crypto payment._

Shawn writes the code – studied CS in Stanford, founded multiple companies (Redshift, Chainsights, MugglePay). He researched at Virtual Human interaction lab at Stanford. You can check out the codebase here: <<https://github.com/RozoAI/rozo-tap-to-pay>>  
LinkedIn: <https://www.linkedin.com/in/shawn-yu-37498b40/>

Sky co-founded MugglePay with Shawn and leads operations at Rozo. She worked at American Express. She brings deep crypto experience from her time as a blockchain researcher at the Tron Foundation. Sky **co-founded** MugglePay with Shawn in 2019, **helping** over 3,000 merchants accept crypto. MugglePay has processed $50M+ per month on blockchain.  
LinkedIn: <https://www.linkedin.com/in/sky-h-88811b118/>

### Links in the text

- https://dune.com/rozointents/stellar
- https://youtube.com/shorts/bhRke7lmNH0?feature=share
