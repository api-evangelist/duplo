---
name: Collect a payment with Atlas Checkout
description: Initiate a hosted Atlas checkout session, redirect the customer to pay, and confirm the payment via webhook or verification call.
api: Atlas Payments API
base_url: https://atlas.tryduplo.com
operations: [checkoutInitiate, checkoutVerifyByReference, getCheckoutTransactionsBySourceReference]
---

# Collect a payment with Atlas Checkout

Use this to collect a one-off payment from a customer via Atlas's hosted checkout.

## Auth
Send `Authorization: Bearer <YOUR_API_KEY>`. Use a **test** key while developing; a **live** key in production (the key selects the environment). See `authentication/duplo-authentication.yml`.

## Steps
1. **Initiate** — call `checkoutInitiate` with the amount, currency, and your own unique `sourceReference`. It returns a hosted checkout URL.
2. **Redirect** — send the customer to the checkout URL to complete payment.
3. **Confirm** — either:
   - handle the webhook events `CHECKOUT_PROCESSED` then `CHECKOUT_COMPLETED` (see `asyncapi/duplo-atlas-webhooks.yml`), authenticating each event by looking it up with `checkoutVerifyByReference` / `getCheckoutTransactionsBySourceReference`; or
   - poll `checkoutVerifyByReference` using the reference returned at initiation.

## Rules
- Reuse your `sourceReference` for lookups and de-duplication — there is no Idempotency-Key header.
- Webhooks can arrive more than once; key on the reference and process idempotently.
- On error, read the custom envelope `{requestId, requestTimestamp, message, errors, statusCode}` (see `errors/duplo-problem-types.yml`); `requestId` is the support correlation id.
