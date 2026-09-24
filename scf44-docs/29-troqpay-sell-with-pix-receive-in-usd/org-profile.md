Source: https://github.com/troqpay/.github/blob/main/profile/README.md

<div align="center">

# TroqPay

**Sell locally. Get paid globally.**

Local payments, global operations, and intelligence on one platform.

[Website](https://troqpay.com) · [Documentation](https://docs.troqpay.com) · [App](https://app.troqpay.com) · [SDK](https://github.com/troqpay/sdk) · [Agent plugin](https://github.com/troqpay/agent-plugin)

</div>

---

Expanding into a new market should not mean rebuilding your financial operation from scratch. TroqPay brings local payments, global movement, balances, and reconciliation into one place. Start with a payment link and move to the API when you need more control.

## Sell, operate, and grow

### Sell locally

Accept the payment methods customers already use through hosted checkout, payment links, API, and webhooks. TroqPay supports Pix in Brazil and SPEI in Mexico.

### Operate globally

Use TroqPay to send and receive funds in BRL, USD, EUR, ARS, and MXN, access virtual accounts in USD and EUR, and manage balances in USDC and USDT.

### Decide with data

Elisa brings revenue, conversion, costs, and operational signals together so teams can see what deserves attention next.

Availability depends on the product, market, eligibility, and partner coverage.

## Built for developers and AI agents

- **[JavaScript/TypeScript SDK](https://github.com/troqpay/sdk)** — create checkouts, retrieve balances, and automate payment flows with [`@troqpay/sdk`](https://www.npmjs.com/package/@troqpay/sdk).
- **[Agent plugin](https://github.com/troqpay/agent-plugin)** — official MCP server and plugin for Codex, Claude, and other MCP clients.
- **[Documentation](https://docs.troqpay.com)** — quickstarts, API reference, checkout flows, webhooks, and integration guides.
- **[LLM-friendly documentation](https://troqpay.com/llms.txt)** — structured product context for AI tools.

## Quickstart

Install the SDK in a backend application:

```bash
npm install @troqpay/sdk
```

```ts
import { Troqpay } from "@troqpay/sdk";

const troqpay = new Troqpay({
  apiKey: process.env.TROQPAY_API_KEY!,
});

const checkout = await troqpay.checkouts.create(
  {
    amount: 12990,
    description: "Pro plan",
    externalId: "order_1001",
  },
  {
    idempotencyKey: "order_1001",
  }
);

console.log(checkout.checkoutUrl);
```

TroqPay API keys are private credentials. Store them securely in your server environment and never include them in frontend or mobile app code.

## Start building

1. Create an account and generate a test API key at [app.troqpay.com](https://app.troqpay.com).
2. Follow the [quickstart](https://docs.troqpay.com/quickstart) to receive your first `checkout.paid` webhook.
3. Explore the [API reference](https://docs.troqpay.com/api-reference) and the public repositories below.

## Public repositories

- [`troqpay/sdk`](https://github.com/troqpay/sdk) — official JavaScript and TypeScript SDK.
- [`troqpay/agent-plugin`](https://github.com/troqpay/agent-plugin) — official MCP server and agent plugin.
- [`troqpay/.github`](https://github.com/troqpay/.github) — organization profile and community health files.

## Links

- [Website](https://troqpay.com)
- [Documentation](https://docs.troqpay.com)
- [App](https://app.troqpay.com)
- [LinkedIn](https://linkedin.com/company/troqpay)
- [Instagram](https://instagram.com/troqpay)
- [X](https://x.com/troqpay)

---

TroqPay provides technology infrastructure for payments and digital assets. TroqPay is not a bank. Regulated financial services are provided by duly authorized and regulated partners, where applicable.
