---
name: Send a bank payout
description: Resolve the destination bank and account, then initiate an Atlas bank-transfer payout and track it to settlement.
api: Atlas Payments API
base_url: https://atlas.tryduplo.com
operations: [bankList, nameEnquiry, createPayoutBankTransfer, getPayoutByReference]
---

# Send a bank payout

Use this to pay a recipient's bank account from your Atlas wallet.

## Auth
Send `Authorization: Bearer <YOUR_API_KEY>` (test vs live selected by the key). In test mode, first fund the test wallet with `fundWallet` (see `sandbox/duplo-sandbox.yml`).

## Steps
1. **List banks** — call `bankList` for the destination currency to get the correct bank code.
2. **Resolve the account** — call `nameEnquiry` with the bank code + account number to confirm the account name before sending money.
3. **Pay** — call `createPayoutBankTransfer` with the amount, resolved account, bank code, and your own unique `sourceReference`.
4. **Track** — poll `getPayoutByReference`, or handle the payout webhooks `OUT_FLOW_PENDING_EVENT` → `OUT_FLOW_SUCCESS_EVENT` / `OUT_FLOW_FAILED_EVENT` (see `asyncapi/duplo-atlas-webhooks.yml`).

## Rules
- Always run `nameEnquiry` before paying — payouts are irreversible.
- Reuse your `sourceReference` to look up and de-duplicate the payout (no Idempotency-Key header).
- Authenticate payout webhooks by looking up `data.reference` against the transactions API; handle duplicates idempotently.
- Errors use the custom envelope in `errors/duplo-problem-types.yml`.
