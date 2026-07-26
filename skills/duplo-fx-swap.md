---
name: Swap between currency pairs
description: Check the live exchange rate for a currency pair, execute an FX swap on Atlas, and confirm the resulting wallet balances.
api: Atlas Payments API
base_url: https://atlas.tryduplo.com
operations: [getExchangeRates, initiateFxSwap, getFxWalletBalances]
---

# Swap between currency pairs

Use this to convert funds between two supported currencies held on Atlas.

## Auth
Send `Authorization: Bearer <YOUR_API_KEY>` (test vs live selected by the key). See `authentication/duplo-authentication.yml`.

## Steps
1. **Quote** — call `getExchangeRates` for the source→target currency pair to read the live rate.
2. **Swap** — call `initiateFxSwap` with the pair and amount (and your own `sourceReference`). Watch for `FX_SWAP_CREATED` then `FX_SWAP_COMPLETED` webhooks.
3. **Confirm** — call `getFxWalletBalances` to verify the debited source and credited target balances.

## Rules
- Rates move; re-quote with `getExchangeRates` immediately before swapping.
- Reuse your `sourceReference` for lookup/de-duplication.
- Errors use the custom envelope in `errors/duplo-problem-types.yml`.
