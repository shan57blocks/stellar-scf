Source: https://useblinkapp.com/doc

How to Use Blink | Blink
BlinkFeaturesHow it worksChainsFAQ
Download app
Official Documentation
How to use Blink
The complete guide to setting up and using the Blink mobile app for real-world crypto payments. We rely on Bluetooth Low-Energy to guarantee seamless checkouts across iOS and Android.
Architecture Update: Bluetooth over NFC
To provide standard, cross-platform compatibility across all mobile devices without OS-level restrictions (like Apple's closed NFC constraints), Blink utilizes Bluetooth Low-Energy (BLE) as the primary tap-to-pay mechanism, backed by a QR code fallback.
For Payers (Customers)
1. Enable Bluetooth
Ensure your smartphone's Bluetooth is turned on. When you're ready to check out, simply open the Blink App.
2. Tap to Receive Request
Bring your phone close to the merchant's device. Blink will automatically detect the local Bluetooth payment broadcast and render the checkout screen instantly.
3. Approve Transaction
Review the exact fiat equivalent mapping to your chosen crypto asset. Use Face ID or biometrics to securely sign the transaction strictly locally on your device.
4. Instant Settlement
The transaction is pushed permanently onchain (Stellar, Base, Solana, etc). Confirmation typically executes in under 5 seconds. You're good to go!
For Receivers (Merchants)
1
Input the Bill Amount
On the Blink App, type in the final charge amount natively in your preferred fiat currency. The system handles real-time oracle exchange rates.
2
Broadcast the Payment Request
Press “Receive”. Blink will instantly activate a Bluetooth Low-Energy beacon bridging data to any proximate customer phone. A Scan-to-Pay QR code will also be actively displayed on screen for cross-compatibility fallback.
3
Wait for User Signature
The App listens momentarily while the customer signs on their own hardware. Do not close the screen.
4
Fiat Finality
Once the blockchain states finalize, Blink's smart bridges swap the incoming crypto for fiat immediately and flag your interface with a glowing green success screen. You acquire zero volatility exposure.
Coming SoonIntegration SDKs
Looking to deploy Blink at your physical retail location or embed our SDK deep into your custom web architecture? SDK access and documentation will be rolling out soon.
Developer Docs Upcoming
BlinkNon-custodial crypto payments at the counter via Bluetooth, settled in local currency.
How to useDocsDunesTerms of ServicePrivacy PolicyCookie PolicyDelete accountCookie Settings
Money you control
XLinkedInInstagramTelegram
© 2026 Blink Labs Ltd
