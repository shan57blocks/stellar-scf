# ElementPay: Stablecoin rails for  the Global South.

Source: https://communityfund.stellar.org/submissions/rech5LZCLUxZCNwaa (SCF #44, awarded $90.0K, category: Financial Protocols)

- Website: https://elementpay.net
- Architecture doc: https://docs.google.com/document/d/1CvOux9kW4VyKa84TgohxSKK_grFUPNvK/edit?usp=sharing&ouid=103974570592430504718&rtpof=true&sd=true

## Links in the submission

- https://docs.google.com/document/d/1CvOux9kW4VyKa84TgohxSKK_grFUPNvK/edit?usp=sharing&ouid=103974570592430504718&rtpof=true&sd=true
- https://drive.google.com/drive/folders/1IoyP5JofRPQZ-8lEk6034BqVs1HyoLz1?usp=sharing

## Submission text (as published)

```text
Products & Services
ElementPay provides stablecoin invoicing, collections, payouts, local settlement, and reconciliation tools for businesses operating across Africa and the Global South. With Stellar, we will enable businesses to create and settle stablecoin invoices through Stellar accounts and assets, allowing faster and lower-cost payment flows.
For collections, Stellar will be used to receive stablecoin payments, monitor transactions, and update payment status through ElementPay APIs and webhooks. For payouts, Stellar will support low-cost transfers before funds are settled to local rails such as mobile money or bank accounts. For reconciliation, Stellar transaction data will be mapped to invoices, payout IDs, customer references, and settlement records inside the ElementPay dashboard.
These improvements will make ElementPay faster, more scalable, and more reliable for enterprise customers, while bringing real payment volume, business use cases, and local settlement utility to the Stellar ecosystem.
Requested Budget
$90.0K
Traction Evidence
ElementPay is live with early commercial traction. We currently serve 50 businesses and over 2,000 users. Our dashboard has tracked KES 3.95M in Kenya on-platform volume across 4,563 transactions. We are also processing around $60K in monthly total volume and have facilitated over $5M in OTC trades.
We have built and launched core infrastructure for on/off-ramping, payouts, mobile money settlement, USDC invoicing, enterprise APIs, reconciliation, and multi-chain liquidity operations. We are currently expanding our infrastructure to support additional African markets, including Nigeria, Ghana, South Africa, and Uganda.
ElementPay is backed by ecosystem partners including Base, Lisk, XFounders, and AyaHQ. We also have LOIs and critical partnerships in progress that are expected to unlock up to $20M in transaction volume within 90 days once activated.
For traction evidence, we will provide a Google Drive folder containing dashboard screenshots, product/demo links, website link, transaction or explorer links where available, LOIs or partnership evidence with sensitive details redacted, and ecosystem validation from Base, Lisk, XFounders, and AyaHQ.
Google Drive evidence folder: [
https://drive.google.com/drive/folders/1IoyP5JofRPQZ-8lEk6034BqVs1HyoLz1?usp=sharing
]
Tranche 1 (Deliverable Roadmap) - MVP
Building the Stellar foundation layer inside ElementPay. This includes integrating Stellar Wallets Kit for wallet connection, adding Stellar account support in the backend, enabling stablecoin asset transfer support, and building transaction monitoring for Stellar payments. We will also map Stellar transaction IDs to ElementPay’s internal invoice, collection, payout, customer, and settlement references so Stellar activity can be tracked inside our existing business infrastructure.
Completion will be measured by a working Stellar wallet connection flow, backend support for Stellar accounts and asset transfers, successful test transactions, transaction monitoring, internal reference mapping, and dashboard visibility for Stellar transactions. Businesses should be able to see Stellar payment activity linked to ElementPay transaction records.
Budget:
$18,000
Tranche 2 (Deliverable Roadmap) - Testnet
Integrating Stellar Disbursement Platform and Aquarius to support enterprise payout and liquidity use cases. Stellar Disbursement Platform will be used for business payout and bulk disbursement flows, while Aquarius will provide liquidity visibility and routing insights for Stellar-based settlement activity. This tranche will focus on payout creation, payout status tracking, webhook updates, reconciliation records, and dashboard reporting for businesses using Stellar-supported payouts.
Completion will be measured by the ability to create Stellar-supported payout batches, track payout status, receive webhook updates, generate reconciliation records, and display payout activity in the ElementPay dashboard. Aquarius integration will be complete when ElementPay can access relevant liquidity and routing data for Stellar settlement flows.
Budget:
$27,000
Tranche 3 (Deliverable Roadmap) - Mainnet
Completing the cross-chain USDC settlement layer and launch the Stellar integration into a production-ready environment. This includes integrating CCTP for native USDC movement between Stellar and supported external chains, connecting Stellar payment flows to ElementPay’s existing invoicing, collections, payouts, and reconciliation system, completing pilot testing with selected businesses, and preparing technical documentation.
Completion will be measured by a functional CCTP-supported USDC transfer flow, end-to-end Stellar-supported invoicing, collections, payouts, transaction monitoring, and reconciliation inside ElementPay. Selected pilot businesses should complete test transactions, view settlement status, and access transaction records through the dashboard or APIs. Final completion will be mainnet or production-equivalent launch of Stellar-supported payment flows inside ElementPay.
Budget:
$35,999
Team
ElementPay Team
ElementPay brings together expertise in blockchain infrastructure, fintech engineering, artificial intelligence, payment operations, strategic partnerships, and African market execution. The team combines strong technical capabilities with practical experience building, integrating, and operating financial products across emerging markets.
Joseph Thuku, Co-Founder and CEO
Joseph Thuku is a software engineer and technology entrepreneur with more than six years of experience across blockchain infrastructure, AI systems, networking, developer platforms, and financial technology.
At ElementPay, Joseph leads company strategy, product vision, technical architecture, and partnerships. He oversees the development of ElementPay’s developer APIs, smart-contract systems, and multi-chain stablecoin settlement infrastructure connecting global digital assets with African payment rails.
Joseph is an active builder in the global Web3 ecosystem and has won multiple blockchain hackathons, including ICP Hackathon, ETHSafari, ETH Ethiopia, and the Base Around the World Builderthon. He was also selected for the Hashed Vibe Haus Nairobi cohort, powered by Tether, alongside high-potential founders receiving support from experienced operators, ecosystem partners, and investors.
Before ElementPay, Joseph co-founded Rentease, a property-technology platform that used geolocation and machine learning to improve rental-property discovery. He also worked with Aly at Revolution Analytics, where they built software products and established the technical working relationship that now supports ElementPay.
Joseph holds a Bachelor of Science in Information Technology from Mount Kenya University and completed Software Engineering training through ALX Africa.
Profiles
LinkedIn:
https://www.linkedin.com/in/joseph-thuku-01898b208/
GitHub
:
https://github.com/JosephThuku
X
:
https://x.com/devjoethuku
Aly Mohamed Mtsumi, Co-Founder and CTO
Aly Mohamed Mtsumi is a full-stack engineer with more than eight years of experience across cloud infrastructure, fintech systems, backend engineering, APIs, and scalable financial platforms.
At ElementPay, Aly leads engineering and infrastructure. He oversees payment-provider integrations, transaction processing, reconciliation systems, production deployment pipelines, blockchain integrations, and the backend infrastructure powering ElementPay’s settlement APIs.
Before ElementPay, Aly served as Technical Lead at Revolution Analytics, where he led engineering teams building enterprise cloud and financial software systems. He also founded Isafari, an AI-powered wildlife prediction and tracking platform focused on Africa.
Aly is active in the African Web3 ecosystem and has spoken at the DePIN Summit Africa about blockchain-powered financial systems and decentralized infrastructure for emerging markets. His participation in programs such as Web3Bridge and the XFounders Accelerator has strengthened his experience in Web3 infrastructure, startup execution, and scaling technology products across international markets.
Profiles
LinkedIn:
https://www.linkedin.com/in/aly-mtsumi-588627143/
GitHub
:
https://github.com/Mtsumi
Bivvon Kinanga, Co-Founder and Chief Operating Officer
Bivvon Kinanga is a fintech and Web3 operations professional with experience across digital assets, payment operations, ecosystem development, business growth, partnerships, and customer coordination.
At ElementPay, Bivvon leads operations, growth, and strategic partnerships. He works with payment providers, liquidity partners, enterprise customers, and ecosystem stakeholders to expand ElementPay’s market presence and support reliable cross-border collections, payouts, and settlements.
Bivvon also coordinates customer onboarding, partnership development, operational execution, and payment-counterparty relationships. His market-facing role helps ensure that ElementPay’s technology addresses the practical payment and settlement challenges faced by African businesses.
Before ElementPay, Bivvon gained hands-on Web3 market experience as a brand ambassador for platforms including Binance, Worldcoin, Yellow Card, NoOnes, and Bitget. This provided practical exposure to community development, customer acquisition, digital-asset adoption, and the realities of introducing financial products across African markets.
He also co-founded QUBEQRAFT Technologies, a technology company working across web development, product design, and UI/UX.
Profile
LinkedIn:
https://www.linkedin.com/in/bivvon-kinanga-56488a257/
Nelson Kamau, Founding Engineer
Nelson Kamau is a software engineer and machine-learning specialist with experience across frontend development, full-stack systems, data science, and AI-powered software solutions.
At ElementPay, Nelson contributes to the development of the company’s payment infrastructure and customer-facing products. He works with the founding engineering team to deliver reliable product features, strengthen system integrations, improve platform performance, and support secure and scalable financial services.
Nelson’s software engineering and applied AI experience strengthens ElementPay’s ability to develop intelligent reconciliation, transaction monitoring, operational automation, data-analysis, and risk-management capabilities.
His technical strengths complement Joseph’s blockchain architecture experience and Aly’s backend and cloud-infrastructure leadership, giving ElementPay engineering coverage across blockchain settlement, payment APIs, cloud infrastructure, product engineering, and intelligent financial systems.
Profiles
LinkedIn:
https://www.linkedin.com/in/nelson-kamau-3749681b1/
GitHub
:
https://github.com/MwangiNelson
Patrick Lemay, Advisor to the Board
Patrick Lemay is an international payments and fintech executive with more than 15 years of experience across payment infrastructure, product strategy, corporate development, mergers and acquisitions, international expansion, and strategic partnerships.
Patrick has held senior leadership roles at global payment companies, including Paysafe and PayRetailers. His experience spans product leadership, business development, corporate strategy, payment-sector acquisitions, and the expansion of financial platforms across international markets.
As an advisor to ElementPay, Patrick provides strategic guidance on payment infrastructure, commercial strategy, partnerships, fundraising, regulatory positioning, and market expansion.
His international payments and corporate-development experience complements the founding team’s technical and operational capabilities, helping ElementPay navigate complex payment ecosystems while building sustainable infrastructure connecting stablecoins with regional payment systems across emerging markets.
Profile
LinkedIn:
https://www.linkedin.com/in/patricklemay/
Bivvon kinanga
```
