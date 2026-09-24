Source: https://drive.google.com/file/d/1PntPBYAJAFpx4VqRyJIK7GUjG8CepA2l/view (troqpay-pitch-deck-v2-web-summit-rio-2026.pdf, in the evidence folder https://drive.google.com/drive/folders/1lqU6xWZp2AJaK6fVubzF9IQzVni3gRD5)

Transcribed from the slide images. The PDF says 13 slides; 12 pages were readable.

# TroqPay pitch deck (Web Summit Rio, June 2026)

## 1. Title
Sell with Pix and get paid in BRL or USD stablecoins. Simple payments for SaaS, services, and creators. troqpay.com

## 2. The problem
AI has accelerated creation, but getting paid in stablecoins is still complex.
- Integration: payment APIs were not built for vibe-coders and AI agents. Confusing docs and long flows slow down anyone trying to integrate and sell fast.
- Sales intelligence: creators and service providers still operate with limited visibility into sales, customers, and recurring revenue. They need links, dashboards, and easy-to-track data.
- Payouts: sellers in Brazil want to charge locally and choose how they get paid to protect their purchasing power: digital dollars or BRL, with clear balances, fees, and withdrawals. Easier onboarding to stablecoins (USDT, USDC).

## 3. The solution
TroqPay is simple payment infrastructure to charge with Pix, sell by link or API, and get paid in USDT or BRL. Launch fast: payment links, API, and simple webhooks. Chat with your business, understand your customers, and turn sales data into decisions.
- Links: to sell fast
- API: simple REST + webhooks
- Intelligence: sales data for better decisions
- USDT/BRL: flexible payouts

## 4. Architecture
With TroqPay, sell locally and choose how you get paid.
Seller (creates a link or integrates the API) -> TroqPay (payment links, Pix API, checkout, webhooks, dashboard, intelligence) -> customer pays with Pix; regulated partners process it (payment institutions/VASPs); withdraw USDT or BRL.

## 5. Who uses it
- Builders and micro-SaaS: developers, vibe-coders, and founders who need to integrate Pix fast, receive reliable webhooks, and monetize products, agents, and automations.
- Creators and digital content: educators, communities, and creators selling access, courses, subscriptions, or digital products.
- Professionals and service providers: consultants, psychologists, mentors, freelancers, and specialists who want to charge with Pix and track payments.

## 6. Market: three major shifts
- 6.89M developers on GitHub in Brazil (4th largest developer community on GitHub).
- 42% of online purchases in Brazil are already paid with Pix; expected to reach 50% of online transactions by 2028.
- US$ 28T stablecoin economic volume in 2025.
Sources on slide: GitHub Octoverse 2025; EBANX/PCMI via Reuters 2026; Chainalysis 2026.

## 7. Product
Sell by link. Integrate via API. Make better decisions.
- Checkout: dynamic Pix QR code, payment page, and links.
- REST + webhooks: simple API, no mandatory SDK.
- Dashboard + reconciliation: charges, customers, payment status, balances, history.
- Complete sandbox environment: simulated Pix, webhooks, separate test/live keys.
Example shown: `POST https://api.troqpay.com/v1/checkouts` with amount 4990, currency BRL, description, externalId and an Idempotency-Key header.

## 8. Why TroqPay
Start with links, scale with APIs. Data to decide. Flexible payouts: sell in BRL and withdraw in digital dollars or BRL.

## 9. Competitors
| | Brazilian PSP | VASP / exchange | TroqPay |
|---|---|---|---|
| Pix checkout for merchants | yes | no | yes |
| Links, checkout, dashboard | yes | no | yes |
| Simple API for builders | partial | no | yes |
| Sales and payout data | partial | no | yes |
| Payouts | BRL | crypto | BRL or USDT |

## 10. Roadmap
- Today: payment links, Pix checkout, API, webhooks, sandbox, business intelligence, USDT or BRL payouts.
- TroqPay 1, 0-6 months: credit card, subscriptions, split payments, automatic withdrawals, refunds, advanced reconciliation.
- TroqPay 1, 6-12 months: integrations with tools used by the ideal customer.
- TroqPay 2 vision: a global account with a card; multi-currency balances, swap, transfers between users, international card, partner-powered yield.

## 11. Business model
1% take rate on approved Pix payments, minimum R$ 0.99 per transaction, no monthly fee. BRL withdrawal R$ 2.99. USDT withdrawal: final conversion rate.

## 12. Team
- Vitor Pio, Founder and CEO (ex-Samsung R&D, MSc Computer Science UFF).
- Michael Guimaraes, CTO (over a decade in crypto; software engineer and product owner).
- Jamile Lima Pio, COO (ex-CI&T).
