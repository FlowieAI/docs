---
name: check-einvoice-reachability
description: Check whether a company can receive electronic invoices, and on which network, before promising delivery. Accepts a VAT number, SIRET, or Peppol participant identifier. Use before sending to a new counterparty.
---

# Check e-invoicing reachability

## When to use this

Before promising or attempting delivery to a **new counterparty**. A company
that is not registered on any network cannot receive an electronic document, and
finding that out at send time wastes a cycle and may leave a document stuck.

## Verify a recipient

```bash
curl -X POST https://back.flowie.ink/exchange/v1/directory/verify   -H "Authorization: Bearer $KEY" -H "Content-Type: application/json"   -d '{"recipient":"0208:0000000002","documentType":"invoice"}'
```

## Search the directory

Covers the Peppol directory and the French PPF annuaire:

```bash
curl "https://back.flowie.ink/exchange/v1/directory/search?query=BE0123456789"   -H "Authorization: Bearer $KEY"
```

## With the SDK

```js
import { FlowieExchange } from "flowie-exchange";
const flowie = new FlowieExchange({ apiKey: process.env.FLOWIE_API_KEY });
const reach = await flowie.verifyRecipient({ recipient, documentType: "invoice" });
```

## Notes

- Identifiers accepted: bare Peppol id (`0208:0123456789`), `vat:…`, `comp_…`,
  SIRET. They are normalised to the canonical Peppol id.
- Reachability is per **document type** — a party may receive invoices but not
  orders.

## Reference

https://docs.get-flowie.com/reference/ · https://docs.get-flowie.com/compliance/
