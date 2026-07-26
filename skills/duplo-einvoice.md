---
name: Issue an NRS-compliant e-invoice
description: Onboard the taxpayer, create an e-invoice, validate and digitally sign it, and confirm it for Nigeria Revenue Service (NRS) compliance.
api: Atlas Payments API
base_url: https://atlas.tryduplo.com
operations: [onboardTaxpayer, createEInvoice, signEInvoice, validateEInvoice, confirmEInvoice]
---

# Issue an NRS-compliant e-invoice

Use this to generate a Nigeria Revenue Service (NRS) compliant electronic invoice.

## Auth
Send `Authorization: Bearer <YOUR_API_KEY>` (test vs live selected by the key). See `authentication/duplo-authentication.yml`.

## Steps
1. **Onboard** — call `onboardTaxpayer` once to register the taxpayer for NRS e-invoicing.
2. **Create** — call `createEInvoice` with the invoice line items, customer, and your own `sourceReference`.
3. **Validate** — call `validateEInvoice` to check the invoice against NRS rules.
4. **Sign** — call `signEInvoice` to digitally sign the validated invoice.
5. **Confirm** — call `confirmEInvoice` to finalize it.

## Rules
- Validate before signing; sign before confirming — the NRS flow is ordered.
- Reuse your `sourceReference` to fetch the invoice (e.g. `getEInvoiceBySourceReference`) and avoid duplicates.
- Errors use the custom envelope in `errors/duplo-problem-types.yml`.
