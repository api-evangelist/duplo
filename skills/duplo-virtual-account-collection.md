---
name: Create a customer with a virtual account and reconcile inflows
description: Create an Atlas customer with a dedicated virtual account, then reconcile bank-transfer inflows via webhook and the transactions API.
api: Atlas Payments API
base_url: https://atlas.tryduplo.com
operations: [createNewCustomer, findCustomerByReference, getTransactions]
---

# Create a customer with a virtual account and reconcile inflows

Use this to give a customer a dedicated virtual account so they can pay you by bank transfer.

## Auth
Send `Authorization: Bearer <YOUR_API_KEY>` (test vs live selected by the key). See `authentication/duplo-authentication.yml`.

## Steps
1. **Create the customer** — call `createNewCustomer` with the customer details and your own `sourceReference`. Atlas provisions a dedicated virtual account for the customer.
2. **Retrieve** — call `findCustomerByReference` to read back the customer and its virtual account details to display to the payer.
3. **Reconcile inflows** — when the customer funds the account you receive the `IN_FLOW_SUCCESS_EVENT` webhook (see `asyncapi/duplo-atlas-webhooks.yml`). Authenticate it by querying `getTransactions` (or by the event `data.reference`) before crediting your ledger.

## Rules
- Authenticate every webhook by looking the reference up against the transactions API — Atlas does not sign webhooks.
- Handle duplicate `IN_FLOW_SUCCESS_EVENT` deliveries idempotently by keying on the transaction reference.
- Errors use the custom envelope in `errors/duplo-problem-types.yml`.
