Source: https://docs.google.com/document/d/1GCSqjQPSkfZWO5Te1NIDxaC1H7ntl2GhPM8j4U2IBQ8/edit?usp=drivesdk

(Architecture document from JUMPA's earlier SCF #43 submission, exported as plain text.)

﻿JUMPA Technical Architecture Document  (with Visual Diagram Description)






JUMPA - Technical Architecture Overview


High-Level Goal 


Jumpa is a chat-first, intent-based payment platform that lets users spend stablecoins like cash in the real world across 70+ countries. It completely abstracts blockchain and banking complexity behind a simple conversational interface, using Stellar  as the core settlement layer.










1. Visual Architecture Diagram Description




                          ┌──────────────────────────────┐
                          │       USER INTERFACES        │
                          │   Chat (Web/Mobile) + USSD   │
                          └──────────────┬───────────────┘
                                         │
                                         ▼
                          ┌──────────────────────────────┐
                          │     INTENT ENGINE (TS)       │
                          │  Natural Language → Actions  │
                          └──────────────┬───────────────┘
                                         │
               ┌─────────────────────────┴─────────────────────────┐
               │                                                   │
               ▼                                                   ▼
   ┌──────────────────────┐                           ┌──────────────────────┐
   │   AUTH & KEY MGMT   │                           │   ORCHESTRATION     │
   │ Passkeys + TEE Split │◄─────────────────────────►│  Routing + Logic    │
   └──────────────────────┘                           └──────────────────────┘
               │                                                   │
               ▼                                                   ▼
   ┌──────────────────────┐                           ┌──────────────────────┐
   │   STELLAR LAYER      │                           │   FIAT RAILS         │
   │ - SEP-1 / SEP-10     │                           │ - Switch (Primary)   │
   │ - SEP-12 / SEP-24    │                           │ - Multi-provider     │
   │ - Soroban Contracts  │                           │   Routing            │
   │   (Shared Wallets,   │                           │ - Local Methods      │
   │    Bill Split, RWA)  │                           │   (Mobile Money,     │
   └──────────────────────┘                           │    Bank, QR)         │
               │                                                   │
               └──────────────────────┬────────────────────────────┘
                                      │
                                      ▼
                          ┌──────────────────────────────┐
                          │       SETTLEMENT & FINALITY   │
                          │       Stellar Network         │
                          └──────────────────────────────┘
                                      │
                                      ▼
                          ┌──────────────────────────────┐
                          │   EXTERNAL SERVICES          │
                          │ - Virtual Cards (USD/EUR)    │
                          │ - Tokenized RWAs             │
                          │ - AML / KYT Monitoring       │
                          │ - Notifications & Indexer    │
                          └──────────────────────────────┘
```


Diagram Legend:
- Top: User interacts only via Chat or USSD (no wallet needed)
- Middle: Intent Engine translates natural language into actions
- Left: Secure Stellar + Soroban layer (settlement & smart contracts)
- Right: Fiat rails powered by Switch + multi-provider routing
- Bottom: Final settlement + supporting services




2. Backend & Business Logic


• Language: TypeScript (Node.js / NestS)
• Intent Engine: Converts chat messages into
Stellar/Soroban transactions
• Routing & Orchestration Layer: Smart provider selection for on/off-ramps
• Key Management: Secure abstraction (device + cloud/TEE split reconstruction)
Blockchain Layer (Stellar)
• Settlement: Stellar Network (tast, low-cost)
• Smart Contracts: Soroban for shared wallets, bill splitting, recurring payments, and tokenized RWAs
• Anchor Compliance:
• SEP-1 (stellar.toml)
• SEP-10 (Web Authentication)
• SEP-12 (Tiered KYC)
• SEP-24 (Interactive Hosted Deposits &
Withdrawals)
• SEP-31 (planned for cross-border)




3. Technology Stack Summary


| Layer                | Technology                              |
|----------------------|-----------------------------------------|
| Frontend             | TypeScript, Next.js, React Native      |


| Backend & Intent     | TypeScript, Next.js               


| Blockchain           | Stellar Network + Soroban Smart Contracts |


| On/Off-Ramp          | Switch (primary) + multi-provider routing |


| Authentication       | Passkeys, Google, Email/Phone + TEE key abstraction |


| Database             | PostgreSQL / MongoDB                    |


| Hosting & Infra      | Cloud (AWS/GCP) + Stellar Anchors       |




4. Core Data Flow Example


1. User types in chat: “Send 350 baht to Bangkok”  
2. Intent Engine parses → creates payment intent  
3. Routing Layer selects best provider (**Switch**)  
4. Soroban contract handles logic (if group/shared)  
5. Transaction settles on Stellar  
6. Merchant receives local currency in bank account  
7. User gets instant confirmation in chat