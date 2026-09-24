Source: https://github.com/WildorAP/verso-tad/blob/main/technical-architecture-document.md

# VERSO Stellar Anchor — Technical Architecture Document


---

## 1. Executive Summary

VERSO is a regulated stablecoin exchange operating in Peru since September 2022, registered as a VASP with UIF-SBS under Resolución SBS N° 02648-2024. We have processed ~$24M USD in gross exchange volume since founding —SUNAT (RUC 20609951088). 2025 volume: $8.8M (+45% YoY). Jan–Apr 2026: $4.9M — **+144% YoY** versus Jan–Apr 2025. April 2026 record month: $1.4M. 2026 annualized run rate: ~$14.6M.

**Proposal:** VERSO deploys the SDF's open-source Anchor Platform as the SEP protocol layer, connected to VERSO's existing backend via business callbacks. VERSO's compliance pipeline, CCI/CCE banking integration, and three production systems are already in production. The grant funds only the Stellar-native layer.

---

## 2. Existing Infrastructure (already in production)

### 2.1 Three Production Systems

**versotek.io — Client Portal (Django 5.1)**
- Client registration + email verification
- KYC/AML via DIDIT API (webhooks in production)
- Transaction flow + document upload to AWS S3
- AML source of funds declaration per operation

**BASE_DE_CLIENTES — Internal Compliance & Operations (Django)**
- KYC/AML: DIDIT scoring, OpenSanctions PEP/sanctions screening, beneficial ownership
- Multi-currency operations: PEN, USD, USDC, USDT + LatAm currencies
- Automated accounting: balance tracking, ITF calculation, audit trail
- IP geolocation logging (anti-fraud, AML)
- Travel Rule implementation (Art. 24, Res. SBS 02648-2024)

**SUNAT_API — Taxpayer Registry Microservice (FastAPI)**
- Peru's complete taxpayer registry (millions of records) — local database
- Fuzzy search by company name (pg_trgm)
- Sub-millisecond latency — no external API dependency

### 2.2 Current Technology Stack

| Layer        | Technology                                                     |
| ------------ | -------------------------------------------------------------- |
| Backend      | Django 5.1 / Python                                            |
| Frontend     | React, Vite, JavaScript                                        |
| Microservice | FastAPI / Python                                               |
| Database     | PostgreSQL (Railway) + pg_trgm                                 |
| File storage | AWS S3 — AES256 at rest                                        |
| Deployment   | Railway                                                        |
| KYC/AML      | DIDIT API (webhooks) + OpenSanctions (PEP/sanctions screening) |
| Email        | Gmail SMTP                                                     |

**Fiat Rails (Production)**

| Region / Rail                                  | Type                                      | Status                                           |
| ---------------------------------------------- | ----------------------------------------- | ------------------------------------------------ |
| Peru: CCI/CCE — national interbank clearing    | PEN disbursements to any Peruvian bank    | Active — production                              |
| Peru: BCP — Banco de Crédito del Perú          | PEN + USD — collections and disbursements | Active — production (annual compliance verified) |
| Peru: Banco Internacional del Perú (Interbank) | PEN + USD — collections and disbursements | Active — production (annual compliance verified) |

**Compliance & KYC Infrastructure**

| Component | Detail |
|-----------|--------|
| KYC Provider | DIDIT API — active integration, webhook callbacks in production |
| Screening Provider | OpenSanctions — global PEP and sanctions database |
| Screening Type | Per-client onboarding (synchronous) + per-transaction beneficiary check |
| Lists Checked | OFAC SDN, PEPs (global), SUNAT taxpayer registry, adverse media |
| Match Action | Auto-reject high risk; manual review medium risk; Travel Rule recorded on every operation |
| AML Program | SPLAFT — live since 2022. Compliance Officer formally registered on UIF-SBS SISDEL platform (Arts. 5-7, Res. SBS 02648-2024) |
| SBS Examination | Formal supervisory examination completed — AML/CFT processes independently verified by Peru's banking regulator |
| Coverage | 100% of clients + 100% of transaction beneficiaries (on-ramp and off-ramp) |

### 2.3 Current Operational Flow (pre-SCF #44)

![VERSO Current Operational Flow (pre-SCF #44)](assets/verso-current-flow.svg)

### 2.4 Known Gaps — Addressed by This Grant

| Gap | Where today | Resolved in |
|-----|-------------|-------------|
| VERSO not discoverable as Anchor in any Stellar wallet | No stellar.toml published | T1 D1 — SEP-1 |
| No Stellar wallet authentication | No SEP-10 endpoint | T1 D2 — SEP-10 |
| No automated on-ramp or off-ramp for Stellar users | No SEP-24 + Anchor Platform | T2 D1 — SEP-24 |
| No real-time PEN/USDC quote in Stellar wallets | No SEP-38 endpoint | T2 D2 — SEP-38 |
| No on-chain USDC movement tracking or reconciliation | No Horizon streaming | T2 D3 — Reconciliation |
| Manual settlement — operator polls bank, no automated alert | No banking notification integration | T3 D2 — CCI/CCE Phase 2 |
| VERSO absent from Stellar Anchor Directory | Not submitted | T3 D3 — Anchor Directory |
| No KMS-backed hot wallet for Stellar signing | No AWS KMS integration | T0 — Infrastructure |

### 2.5 What Changes, What Stays

**What stays identical — grant does not touch:**
- VERSO's three production systems (versotek.io, BASE_DE_CLIENTES, SUNAT_API) — no changes
- CCI/CCE fiat rails — BCP and Interbank remain the settlement layer for all fiat flows
- KYC/AML compliance pipeline (DIDIT + OpenSanctions) — unchanged; extended to cover SEP-10 wallet authentication
- SPLAFT/AML program, Travel Rule, beneficial ownership tracking — unchanged; Travel Rule now extends to Stellar transactions
- Business model and fee structure.
- Operations team (treasury/settlement; compliance) — continues managing daily operations

**What changes with the grant:**
- VERSO becomes discoverable as a Stellar Anchor via stellar.toml — visible to every SEP-compatible wallet in the ecosystem
- Stellar wallet users authenticate via SEP-10 and link directly to VERSO's existing KYC records
- On-ramp and off-ramp available through any SEP-24 compatible wallet (Lobstr, Freighter, Vibrant)
- Real-time PEN/USDC quotes displayed in wallets before each transaction confirmation (SEP-38)
- Manual bank polling replaced by Horizon streaming event detection — instant alert to operator on USDC receipt
- Phase 2 (T3): semi-automated settlement reduces average settlement time from hours to under 30 minutes
- VERSO becomes the first regulated PEN/USDC Anchor listed in the Stellar Anchor Directory

---

## 3. Stellar Anchor Architecture

### 3.1 Architecture Decision — SDF Anchor Platform

VERSO deploys the **SDF's open-source Anchor Platform** (Java/Kotlin) as the SEP protocol layer. The Anchor Platform handles all SEP protocol logic out-of-the-box. VERSO's backend implements only the **business callback interface** — the fiat logic, compliance, and CCI/CCE settlement.

**Why this approach:**
- SEP-1/10/24/38 protocol is production-tested by the SDF — no need to re-implement
- Stellar wallets (Lobstr, Freighter, Vibrant) are already certified against it
- Reduced implementation risk, faster time-to-mainnet

### 3.2 High-Level Architecture

![VERSO Stellar Anchor — High-Level Architecture](assets/verso-architecture.svg)

### 3.3 How the Callback Integration Works

The Anchor Platform receives requests from Stellar wallets and calls VERSO's backend via HTTP to request business decisions. VERSO never needs to know SEP protocol details — it only handles fiat and compliance logic.

![VERSO Callback Integration Flow](assets/verso-callback-flow.svg)

### 3.4 Business Callbacks VERSO Implements

| Callback                       | Method                 | What VERSO Backend does                                   |
| ------------------------------ | ---------------------- | --------------------------------------------------------- |
| `POST /callbacks/deposit`      | Deposit request        | Returns CCI/CCE bank transfer instructions for the user   |
| `POST /callbacks/withdraw`     | Withdrawal request     | Returns VERSO's Stellar hot wallet address                |
| `PATCH /callbacks/transaction` | Status update          | Triggers compliance screening + CCI/CCE fiat disbursement |
| `GET /callbacks/rate`          | Quote request (SEP-38) | Returns PEN/USDC rate from BASE_DE_CLIENTES               |

### 3.5 Stellar Client Onboarding Flow

VERSO operates B2C — each client is an individual Stellar wallet holder, onboarded directly with full KYC. There is no intermediary partner. VERSO is responsible for KYC of every end user.

1. Client discovers VERSO Anchor via stellar.toml (SEP-1) inside their Stellar wallet (Lobstr, Freighter, Vibrant)
2. Client selects VERSO as their PEN/USDC on/off-ramp provider
3. Anchor Platform executes SEP-10 challenge-response — client proves control of their Stellar account via keypair signature
4. If new client: SEP-24 webview opens — client submits KYC documents (DNI or passport, source of funds declaration) via DIDIT
5. Compliance Officer reviews: DIDIT KYC scoring + OpenSanctions PEP/sanctions screening + beneficial ownership check
6. Approved client is added to backend with their Stellar public key linked to their verified KYC record
7. Client can immediately initiate on-ramp (PEN → USDC) or off-ramp (USDC → PEN) via SEP-24 or SEP-38
8. Each subsequent operation: client's Stellar key is recognized in backend → SEP-10 auth → transaction processed without re-submitting KYC
9. Annual KYC refresh: every active client undergoes periodic due diligence renewal and information update per SPLAFT protocol (Res. SBS 02648-2024)

---

## 4. SEPs Implemented (via Anchor Platform)

| SEP        | Description                          | Implementation                                               |
| ---------- | ------------------------------------ | ------------------------------------------------------------ |
| **SEP-1**  | stellar.toml — Anchor discovery      | Static file hosted at versotek.io/.well-known/stellar.toml   |
| **SEP-10** | Wallet authentication → JWT          | Anchor Platform handles out-of-the-box                       |
| **SEP-24** | Interactive deposit/withdraw webview | Anchor Platform protocol + VERSO React/Vite webview          |
| **SEP-38** | Real-time quotes                     | Anchor Platform calls `/callbacks/rate` → VERSO returns rate |


---

## 5. On-Ramp Flow — PEN/USD → USDC (step by step)

```
1. User opens Lobstr → taps "Deposit USDC" → selects VERSO
2. Lobstr calls Anchor Platform: GET /sep10/auth (challenge)
3. User signs challenge with Stellar keypair → POST /sep10/auth
4. Anchor Platform issues JWT → Lobstr authenticated
5. Lobstr calls Anchor Platform: POST /sep24/transactions/deposit
6. Anchor Platform → POST /callbacks/deposit → VERSO Backend
7. VERSO Backend checks KYC status in BASE_DE_CLIENTES:
   - If existing client → approved
   - If new → SEP-24 webview requests KYC (DIDIT)
8. VERSO Backend returns CCI/CCE transfer instructions + reference code
9. Anchor Platform → opens SEP-24 webview in Lobstr
10. User sees: "Transfer S/3,700 to BCP via CCI to account XXX, ref TXN-001"
11. User makes CCI/CCE bank transfer from any Peruvian bank
12. Phase 1: VERSO operations team confirms receipt manually
    Phase 2 (Tranche 3): semi-automated via banking notification webhook
13. VERSO Backend → Anchor Platform REST API: update transaction {status: "completed"}
14. Anchor Platform → sends USDC from hot wallet to user's Stellar address
15. Lobstr shows: "Deposit complete — 993 USDC received"
```

---

## 6. Off-Ramp Flow — USDC → PEN/USD (step by step)

```
1. User opens Lobstr → taps "Withdraw USDC" → selects VERSO
2. SEP-10 authentication (same as on-ramp)
3. Lobstr calls Anchor Platform: POST /sep24/transactions/withdraw
4. Anchor Platform → POST /callbacks/withdraw → VERSO Backend
5. VERSO Backend returns: VERSO's hot wallet Stellar address + reference
6. SEP-24 webview opens: user enters bank account + CCI for receiving funds
7. User sends USDC to VERSO's hot wallet on Stellar
8. Horizon streaming detects incoming USDC in real time → event fired to VERSO Backend
9. VERSO Backend → Anchor Platform REST API: update transaction {status: "pending_external"}
10. VERSO Backend triggers compliance screening:
    - DIDIT beneficiary screening
    - PEP list check
    - SUNAT_API verification
    - Source of funds validation
11. If approved:
    Phase 1: VERSO team initiates CCI/CCE transfer manually
    Phase 2 (Tranche 3): automated disbursement via banking API
12. Funds arrive at user's bank account (any Peruvian bank)
13. VERSO Backend → Anchor Platform REST API: update transaction {status: "completed"}
14. Lobstr shows: "Withdrawal complete"
```

---

## 7. Custody & Security

### 7.1 Wallet Architecture

| Wallet | Role | Access |
|--------|------|--------|
| **Hot Wallet** | Daily operations — sends/receives USDC | AWS KMS signing — no plaintext keys |
| **Cold Wallet** | USDC reserve — outside active operations | Hardware wallet (Ledger) — dual approval |

### 7.2 Key Management

| Requirement | Implementation |
|-------------|---------------|
| Hot wallet signing | AWS KMS/HSM — API-based signing only |
| No plaintext keys | Never in source code, .env, or disk |
| Cold wallet | Ledger hardware wallet — dual approval protocol |
| Secrets management | AWS Secrets Manager |
| Audit trail | AWS CloudTrail — every signing operation logged |
| Retry logic | Exponential backoff for failed transactions |

**USDC issuer mainnet (Circle):**
`GA5ZSEJYB37JRC5AVCIA5MOP4RHTM335X2KGX3IHOJAPP5RE34K4KZVN`

---

## 8. Treasury Reconciliation

Horizon Streaming API listens in real time to every USDC movement on VERSO's Stellar hot wallet. Each movement is recorded in PostgreSQL and reconciled with on-chain balance. Discrepancy → automatic alert.

### 8.1 Transaction Record Schema

```sql
stellar_tx_hash      VARCHAR(64)    -- Stellar transaction hash
amount_usdc          DECIMAL(18,7)  -- USDC amount
amount_pen           DECIMAL(18,2)  -- PEN equivalent
amount_usd           DECIMAL(18,2)  -- USD equivalent
direction            VARCHAR(10)    -- 'on_ramp' | 'off_ramp'
client_stellar_key   VARCHAR(64)    -- client Stellar public key
kyc_status           VARCHAR(20)    -- 'approved' | 'pending' | 'rejected'
fiat_rail            VARCHAR(20)    -- 'cci_cce_pen' | 'cci_cce_usd'
fiat_status          VARCHAR(20)    -- 'pending' | 'completed' | 'failed'
anchor_callback_id   VARCHAR(64)    -- Anchor Platform transaction reference
created_at           TIMESTAMP
updated_at           TIMESTAMP
```

---

## 9. Technology Stack — Anchor Layer

| Layer | Technology | Notes |
|-------|-----------|-------|
| SEP Protocol | SDF Anchor Platform (Java/Kotlin) | Deployed via Docker on Railway |
| Business Callbacks | Django 5.1 / Python | 4 callback endpoints |
| SEP-24 Webview | React 18 + Vite + TypeScript | VERSO-branded UI inside Lobstr |
| Stellar SDK | stellar-sdk (Python) | Horizon streaming, hot wallet signing |
| Database | PostgreSQL (Railway) | Treasury reconciliation records |
| File storage | AWS S3 (AES256) | KYC documents, transaction receipts |
| Key management | AWS KMS/HSM | Hot wallet signing — no plaintext keys |
| Monitoring | Horizon Streaming API | Real-time USDC event detection |
| Alerts | AWS CloudWatch | Reconciliation discrepancies, tx failures |
| CI/CD | GitHub Actions | Automated deploy to Railway |

---

## 10. Budget & Timeline

**Total requested: $110,000 USD (in XLM equivalent)**

### 10.1 Tranche Structure

| Tranche | % | Amount | Target Date | Deliverables |
|---------|---|--------|-------------|-------------|
| **T0** | 10% | $11,000 | On approval (~Jul 2026) | Infrastructure: SDF Anchor Platform deployed (Docker, Railway), **testnet** environment live, hot/cold wallet structure created, AWS KMS/HSM configured (no plaintext keys), CI/CD pipeline (GitHub Actions → Railway) |
| **T1** | 20% | $22,000 | **31/08/2026** | **SEP-1 + SEP-10 — MVP Testnet.** stellar.toml published at versotek.io/.well-known (zero validation errors in Stellar Lab). SEP-10 wallet authentication live on **testnet**, integrated with BASE_DE_CLIENTES KYC. First end-to-end simulated deposit verifiable on Stellar Expert testnet. |
| **T2** | 30% | $33,000 | **26/10/2026** | **SEP-24 + SEP-38 — Testnet Complete.** SEP-24 interactive on-ramp and off-ramp live on **testnet** (React/Vite webview, 10+ testnet transactions). SEP-38 real-time PEN/USDC quotes live in Stellar wallets on testnet. Horizon streaming reconciliation: 14-day zero-discrepancy report on testnet. |
| **T3** | 40% | $44,000 | **12/12/2026** | **All SEPs → Mainnet Launch.** SEP-1/10/24/38 all live on Stellar **mainnet** with real PEN/USDC flows verifiable on Stellar Expert. CCI/CCE Phase 2 semi-automated settlement (<30 min avg). 10+ real mainnet transactions on-chain. VERSO submitted to Anchor Directory. Security hardening + audit preparation. Public technical documentation published. |

---

## 11. Pre-Mainnet Verification Matrix

Every check below must pass before the mainnet feature flag is switched. T-gate indicates the tranche at which completion is verified.

| Check | What is verified | Pass condition | T-gate |
|-------|-----------------|----------------|--------|
| stellar.toml validation | SEP-1 endpoint at versotek.io/.well-known/stellar.toml | Zero validation errors in Stellar Laboratory | T1 |
| SEP-10 auth loop | Wallet signs SDF challenge → VERSO backend verifies → JWT issued | Full auth loop confirmed with testnet wallet + KYC lookup in BASE_DE_CLIENTES | T1 |
| First simulated deposit | Backend deposit callback cycle end-to-end on testnet | All transaction states tracked; USDC disbursed on testnet; verifiable on Stellar Expert | T1 |
| SEP-24 on-ramp webview | Interactive deposit flow via React/Vite webview on testnet | 10+ testnet on-ramp transactions complete end-to-end | T2 |
| SEP-24 off-ramp webview | Interactive withdrawal flow via React/Vite webview on testnet | 10+ testnet off-ramp transactions complete end-to-end | T2 |
| SEP-38 real-time quotes | PEN/USDC quotes displayed in Stellar wallets before each transaction | 10 consecutive quotes validated against VERSO's live pricing engine — matching results | T2 |
| Horizon reconciliation | Streaming service captures every USDC movement on testnet hot wallet | 14 consecutive days with zero unresolved discrepancies between on-chain balance and internal ledger | T2 |
| AWS KMS signing | No plaintext private keys in source, .env, or disk | Hot wallet signing via KMS API confirmed; AWS Secrets Manager audit clean | T2 |
| Mainnet config prep | All SEP endpoints, wallet addresses, and USDC issuer switched to mainnet values | stellar.toml updated for mainnet; zero errors in Stellar Laboratory; feature flag tested behind switch | T2→T3 |
| Real mainnet transactions | On-ramp + off-ramp with real PEN/USDC on Stellar mainnet | 10+ transactions verifiable on Stellar Expert mainnet | T3 |
| CCI/CCE Phase 2 settlement | Semi-automated fiat settlement operational | 30 consecutive completed transactions with avg settlement ≤ 30 min; tested via CCI/CCE to BCP, Interbank, and additional Peruvian banks | T3 |
| Security hardening | Edge-case and input validation review on all SEP callback endpoints | Invalid SEP-10 signatures, malformed payloads, duplicate tx IDs, webhook replay — all edge cases tested and passing CI | T3 |
| Anchor Directory submission | VERSO listed as active regulated Anchor for PEN/USDC | All Anchor Directory listing requirements completed; submission confirmed | T3 |
| Public documentation | SEP implementation guide, architecture diagram, callback reference, operational runbook | Complete and published in public GitHub repository | T3 |

---

## 12. Deployment & Operations Flow

### 12.1 Build and Deploy Sequence

```
T0 — Infrastructure (~Jul 2026, on grant approval)
  • SDF Anchor Platform (Docker) deployed on Railway — testnet config
  • Testnet Stellar accounts created + USDC trustlines active
  • AWS KMS/HSM configured — hot wallet signing tested (no plaintext keys)
  • Cold wallet (Ledger) dual-approval protocol active
  • CI/CD pipeline live: GitHub Actions → Railway auto-deploy on merge


T1 — MVP Testnet (31/08/2026)
  • stellar.toml published at versotek.io/.well-known/ — zero errors in Stellar Lab
  • SEP-10 wallet authentication integrated with BASE_DE_CLIENTES KYC
  • First simulated deposit verifiable on Stellar Expert testnet
  • Integration guide published on GitHub

T2 — Testnet Complete (26/10/2026)
  • SEP-24 React/Vite webview live and selectable from Stellar wallets on testnet
  • SEP-38 real-time PEN/USDC quotes displayed in wallets before each transaction
  • Horizon streaming: 14-day zero-discrepancy monitoring period completed
  • Mainnet config prepared behind feature flag — switch ready

T3 — Mainnet Launch (12/12/2026)
  • Feature flag switched → mainnet
  • stellar.toml updated for mainnet (zero errors in Stellar Lab)
  • Real PEN/USDC transactions live on Stellar mainnet — verifiable on Stellar Expert
  • CCI/CCE semi-automated settlement operational (<30 min avg)
  • VERSO submitted to Stellar Anchor Directory
  • Security hardening + audit preparation completed
  • Public technical documentation published on GitHub
```



