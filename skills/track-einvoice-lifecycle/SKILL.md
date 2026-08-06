---
name: track-einvoice-lifecycle
description: Read or advance an electronic invoice through its business lifecycle (issued, under review, approved, paid, rejected). In several countries the transition is the receiver's statutory reporting obligation, forwarded to the national platform. Use when approving, rejecting, or reconciling invoices.
---

# Track and advance an invoice lifecycle

## When to use this

Approving, rejecting, marking paid, or reporting the status of a received
invoice. **This is not bookkeeping.** In France, Italy and several other
jurisdictions the lifecycle transition *is* the receiver's statutory reporting
obligation and is forwarded to the national platform. Do not fire transitions
speculatively.

## Read the history

```bash
curl https://back.flowie.ink/exchange/v1/documents/$DOC_ID/lifecycle   -H "Authorization: Bearer $KEY"
```

## Advance it

```bash
curl -X POST https://back.flowie.ink/exchange/v1/documents/$DOC_ID/lifecycle   -H "Authorization: Bearer $KEY" -H "Content-Type: application/json"   -d '{"status":"approved"}'
```

Statuses: `issued`, `under_review`, `approved`, `rejected`, `paid`.

## With the SDK

```js
await flowie.setLifecycle(doc.id, "approved");
await flowie.setLifecycle(doc.id, "paid");
const history = await flowie.getLifecycle(doc.id);   // history.currentStatus
```

## Notes

- A rejection usually needs a reason code; see the errors reference.
- Transitions are ordered — you cannot mark paid a document that was never
  approved, where the destination country enforces the sequence.

## Reference

https://docs.get-flowie.com/reference/ · https://docs.get-flowie.com/auth.md
