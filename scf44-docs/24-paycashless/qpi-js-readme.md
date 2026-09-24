Source: https://github.com/paycashless/qpi.js/blob/HEAD/README.md

# QR Payment Intent
This library allows you to encode and decode QPI data objects.
Note: it won't generate a QR image, it only creates what can be used as the content of the QR code, you'll have use a seperate library for generating QR codes.

## Get started
```bash
npm install @paycashless/qpi.js
```

## `encode`
This function encodes your payment intent data objects into a string that can be easily embeded in a QR code.

```js
  import { QrPaymentIntent } from '@paycashless/qpi.js';

  const dataObjects = {
    version: '01',
    intentType: IntentType.static,
    paymentAccount: {
      schemeIdentifier: 'com.paycashless',
      financialAddress: 'user@example.com'
    },
    merchantCategoryCode: '4111', // transportation
    countryCode: 'NG',
    payeeName: 'GRUBWAYS',
    city: 'ABUJA',
    postalCode: '900231',
    amount: '5000',
    currency: '566', // NGN
    additionalDataObjects: {
      storeLabel: '055',
      customerLabel: 'cust_2bt7pwl20mb25sa',
      terminalLabel: '05',
      reference: '567223',
      narration: 'FOOD DELIVERY',
      channel: '521' // online
    },
    paymentIntentDataObjects: {
      token: "tok_neojvjkwPrrpr9e03hplcag2ig5gpua8"
    },
  }

  const qpi = new QrPaymentIntent();
  const payload = qpi.encode(dataObjects);
  console.log(payload);
```

## `decode`
This function allows you to decode a QR payment intent payload back to readable data objects

```js
import { QrPaymentIntent } from '@paycashless/qpi.js';

const payload = '00020101021236370114grubways61@fbn0015com.paycashless520441115802NG5908GRUBWAYS6005ABUJA6106900231540450005303566627703030550620customer@example.com0702050512PAY_567228030813FOOD DELIVERY110352180590136tok_neojvjkwPrrpr9e03hplcag2ig5gpua80015com.paycashless6304B06F';
const qpi = new QrPaymentIntent();
const dataObjects = qpi.decode(payload);
console.log(dataObjects);
```

## Data Objects
You can find all the possible data objects listed below;

```js
interface DataObjects {
  version: string;
  intentType: IntentType;
  checksum: string;
  payeeAccount: {
    schemeIdentifier: string;
    financialAddress: string;
  };
  merchantCategoryCode: string;
  countryCode: string;
  payeeName: string;
  city: string;
  postalCode: string;
  payeeInfoAltLanguage?: {
    localLanguage: string;
    payeeName: string;
    city: string;
  };
  amount: string;
  currency: string;
  serviceFee?: string;
  percentageServiceFee?: string;
  additionalDataObjects: {
    storeLabel: string;
    customerLabel: string;
    terminalLabel: string;
    loyaltyNumber?: string;
    reference: string;
    narration: string;
    channel: string;
  };
  paymentIntentDataObjects: {
    schemeIdentifier: string;
    token: string;
  },
}
```

> `merchantCategoryCode` data object shall contain MCC as defined by [ISO 18245]

> `countryCode` data object shall contain the country code of the merchant as defined by [ISO 3166-1 alpha-2]

> `additionalDataObjects.channel` has three chracters, each chracter in each position identifies the characteristic of the channel used for the transaction. The possible value of each character and its definition are listed below

> `currency` data object is to conform with [ISO 4217] containing 3 digit numeric representation of the transaction currency

### Channel: First chracter (Media)

| Value      | Meaning |
| ----------- | ----------- |
| "0" | Print - Merchant Sticker |
| "1" | Print - Bill/Invoice |
| "2" | Print - Magazine/Poster |
| "3" | Print - Other |
| "4" | Screen/Electronic - Merchant POS/POI |
| "5" | Screen/Electronic - Website |
| "6" | Screen/Electronic - App |
| "7" | Screen/Electronic - Other |

### Channel: Second chracter (Transaction Location)

| Value      | Meaning |
| ----------- | ----------- |
| "0" | At Merchant premises/registered address |
| "1" | Not at merchant premises/registered address |
| "2" | Remote commerce |
| "3" | Other |

### Channel: Third chracter (Merchant Presence)

| Value      | Meaning |
| ----------- | ----------- |
| "0" | Attended POI |
| "1" | Unattended |
| "2" | Semi attended (Self-checkout) |
| "3" | Other |


STICKER = 033
INVOICE = 133
POSTER = 233
OTHER_PRINT = 333
POS = 433
WEB = 533
APP = 633
OTHER_ELECTRONIC = 733

STICKER_MERCHANT_PRESENT=000
STICKER_MERCHANT_NOT_PRESENT=001
STICKER_SELF_CHECKOUT=002
STICKER_REMOTE_LOCATION=011

INVOICE_MERCHANT_PRESENT=100
INVOICE_MERCHANT_NOT_PRESENT=101
INVOICE_SELF_CHECKOUT=102
INVOICE_REMOTE_LOCATION=111

POSTER_MERCHANT_PRESENT=200
POSTER_MERCHANT_NOT_PRESENT=201
POSTER_SELF_CHECKOUT=202
POSTER_REMOTE_LOCATION=211

OTHER_PRINT_MERCHANT_PRESENT=300
OTHER_PRINT_MERCHANT_NOT_PRESENT=301
OTHER_PRINT_SELF_CHECKOUT=302
OTHER_PRINT_REMOTE_LOCATION=311

POS_MERCHANT_PRESENT=400
POS_MERCHANT_NOT_PRESENT=411
POS_SELF_CHECKOUT=402

WEB_MERCHANT_PRESENT=500
WEB_MERCHANT_NOT_PRESENT=511
WEB_SELF_CHECKOUT=502

APP_MERCHANT_PRESENT=600
APP_MERCHANT_NOT_PRESENT=611
APP_SELF_CHECKOUT=602

OTHER_ELECTRONIC_MERCHANT_PRESENT=700
OTHER_ELECTRONIC_MERCHANT_NOT_PRESENT=711
OTHER_ELECTRONIC_SELF_CHECKOUT=702

