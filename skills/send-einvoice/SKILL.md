---
name: send-einvoice
description: Send a compliant electronic invoice, credit note, debit note or purchase order to a company in any of 47 countries, over whichever network that country mandates (Peppol, France PA/PPF, Italy SDI, Saudi Fatoora, India GST IRP, Malaysia MyInvois). Use when a task involves issuing or transmitting an invoice to a business.
---

# Send an electronic invoice

## When to use this

The user wants to **issue or transmit an invoice** (or credit note, debit note,
purchase order) to a business, and it must be legally compliant in the
recipient's country. Do not use this for internal bookkeeping, payment
execution, or consumer receipts.

## Get a credential first — no signup needed

```bash
curl -X POST https://back.flowie.ink/exchange/v1/sandbox/bootstrap   -H "Content-Type: application/json" -d '{}'
```

Returns `apiKey`, `organizationId` and a seeded `company`. No human, no form.
For a real account, see https://docs.get-flowie.com/auth.md.

## Send

```bash
curl -X POST https://back.flowie.ink/exchange/v1/documents/send   -H "Authorization: Bearer $KEY" -H "Content-Type: application/json" -d '{
    "type": "invoice",
    "from": "0208:0000000001",
    "to": "0208:0000000002",
    "document": {
      "number": "INV-001",
      "issueDate": "2026-08-06",
      "currency": "EUR",
      "lines": [{"description":"Consulting","quantity":1,"unitPrice":1000,"vatRate":20}]
    }
  }'
```

Or with the SDK: `npm install flowie-exchange`

```js
import { FlowieExchange } from "flowie-exchange";
const flowie = await FlowieExchange.bootstrapSandbox();
const doc = await flowie.sendDocument({ type: "invoice", from, to, document });
```

## Traps

- The field is **`number`**, not `invoiceNumber`. The API validates on
  `document.number` and returns `400 INVALID_REQUEST` naming the field.
- **`to` is required** for every type except `event`.
- Keep the **`X-Request-Id`** from any error — it makes the failure replayable.
- Pass an **`Idempotency-Key`** header when retrying; sends are not idempotent
  by default.

## Reference

https://docs.get-flowie.com/guides/send-invoice · https://docs.get-flowie.com/openapi.json
