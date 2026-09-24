Source: https://unpkg.com/blink-checkout-sdk/README.md (npm package blink-checkout-sdk, latest 0.1.7)

# Blink Checkout SDK

Embeddable stablecoin checkout for any website, Shopify store, or React app. Drop
in a button, call `open()`, and a secure popup (an iframe overlay, Paystack-style)
handles the payment. Funds settle to the merchant as Stellar USDC.

## Install

```bash
npm install blink-checkout-sdk
```

Or use the script tag (no build step):

```html
<script src="https://unpkg.com/blink-checkout-sdk"></script>
```

You need a **publishable key** (`pk_...`) from your Blink dashboard → Developer Portal.

## Quick start (vanilla JS / any site)

```html
<button id="pay">Pay with Blink</button>
<script src="https://unpkg.com/blink-checkout-sdk"></script>
<script>
  document.getElementById('pay').addEventListener('click', function () {
    BlinkCheckout.open({
      publicKey: 'pk_live_xxx',
      amount: 25,
      currency: 'USD',
      email: 'customer@example.com',
      reference: 'order_1234',
      metadata: { orderId: '1234' },
      onSuccess: function (data) { console.log('Paid!', data); },
      onClose: function () { console.log('Closed'); },
    });
  });
</script>
```

## npm (bundlers)

```ts
import { open } from 'blink-checkout-sdk';

open({
  publicKey: 'pk_live_xxx',
  amount: 25,
  currency: 'USD',
  onSuccess: (data) => console.log('Paid!', data),
});
```

## React

```tsx
import { BlinkCheckoutButton, useBlinkCheckout } from 'blink-checkout-sdk/react';

// Drop-in button
<BlinkCheckoutButton
  publicKey="pk_live_xxx"
  amount={25}
  currency="USD"
  className="btn"
  onSuccess={(data) => console.log(data)}
>
  Pay $25
</BlinkCheckoutButton>

// Or imperatively
const { open } = useBlinkCheckout();
open({ publicKey: 'pk_live_xxx', amount: 25, onSuccess: console.log });
```

## Existing payment link

If you already created a payment link in the dashboard, skip `publicKey`/`amount`
and just pass its id:

```ts
open({ linkId: 'pl_or_payment_id', onSuccess: console.log });
```

## Shopify

Add to your theme (e.g. `product.liquid` or a custom Buy button):

```html
<script src="https://unpkg.com/blink-checkout-sdk"></script>
<button
  onclick="BlinkCheckout.open({ publicKey:'pk_live_xxx', amount: {{ product.price | divided_by: 100.0 }}, currency:'USD', reference:'{{ product.id }}', onSuccess: () => window.location.reload() })">
  Pay with Blink
</button>
```

## Options

| Option | Type | Notes |
|---|---|---|
| `publicKey` | string | Publishable key. Required unless `linkId` is given. |
| `linkId` | string | Open an existing link instead of creating a session. |
| `amount` | number | Amount to charge (in `currency`). |
| `currency` | string | `USD` or `NGN`. |
| `email`, `name` | string | Prefills the checkout. |
| `reference` | string | Your order id — echoed back in `onSuccess`. |
| `redirectUrl` | string | Redirect here after success (appends `?reference=…&status=success`). |
| `metadata` | object | Extra key/values stored on the payment. |
| `baseUrl` | string | Checkout host. Defaults to the Blink hosted checkout. |
| `apiUrl` | string | API base. Defaults to `${baseUrl origin}/api/v1`. |
| `onSuccess(data)` | fn | Fired when payment is confirmed. |
| `onClose()` | fn | Fired when the popup is dismissed. |
| `onError(err)` | fn | Fired on setup/session errors. |

`open()` returns `{ close() }` so you can dismiss the popup programmatically.

## Local / self-hosted

Point `baseUrl` at your deployment:

```ts
open({ publicKey: 'pk_test_xxx', amount: 25, baseUrl: 'http://localhost:3001' });
```
