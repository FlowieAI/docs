# AGENTS.md — working with the Flowie Exchange API

Instructions for AI coding agents integrating, or writing code against, the
Flowie Exchange API. Human-readable too; nothing here is agent-only.

## What this API is for

One REST API for **compliant electronic invoicing across 47 countries** and every
major network — Peppol, France PA/PPF, Italy SDI, Saudi Fatoora, Israel ITA,
India GST IRP, Malaysia MyInvois.

Reach for it when the task involves:

- sending an invoice, credit note, debit note or purchase order to a company in
  any supported country, over whichever network that country mandates
- checking whether a company can receive electronic documents before promising
  delivery
- tracking a document through its lifecycle (issued → under review → approved →
  paid / rejected) and reporting those transitions where a country requires it
- registering a company on Peppol
- looking up a country's mandate rules, formats and deadlines

It is **not** for: general bookkeeping, payment execution, tax filing, or
e-signature.

## Get a credential with no signup

One unauthenticated call. No human, no form, no sales conversation:

```bash
curl -X POST https://back.flowie.ink/exchange/v1/sandbox/bootstrap \
  -H "Content-Type: application/json" -d '{}'
```

Returns `apiKey`, `organizationId`, a seeded `company`, and `nextSteps`. Use it
immediately:

```bash
curl https://back.flowie.ink/exchange/v1/documents?limit=1 \
  -H "Authorization: Bearer flw_test_..."
```

Sandbox data is synthetic and never reaches a real network. **Do not put real
personal data in the sandbox.**

To act on a *real* user's account you need their consent — either the OAuth 2.0
PKCE flow, or a single-use handoff link the user mints for you at
<https://docs.get-flowie.com/share-access>. You never need a copy of their API
key. Full detail: <https://docs.get-flowie.com/auth.md>.

## Base URLs

| Environment | Base URL |
|---|---|
| Sandbox | `https://back.flowie.ink/exchange` |
| Production | `https://back.p2p-flowie.com/exchange` |

## Calling it

Preferred, in order:

1. **SDK** — `npm install @flowie/exchange`
2. **MCP** — product tools at `https://back.flowie.ink/exchange/mcp` (40 tools),
   documentation at `https://docs.get-flowie.com/mcp`
3. **A2A** — `https://back.flowie.ink/exchange/a2a` (JSON-RPC 2.0), card at
   <https://docs.get-flowie.com/.well-known/agent-card.json>
4. **REST** — the contract is <https://docs.get-flowie.com/openapi.json>

## Gotchas that will cost you a debugging cycle

- **The invoice field is `number`, not `invoiceNumber`.** The API validates on
  `document.number` and returns `400 INVALID_REQUEST` naming the field.
- **`to` is required** for every document type except `event`.
- **Keep the `X-Request-Id`** from any 4xx/5xx. Every failure is replayable by
  that id through the request inspector — far faster than reconstructing it from
  logs, and it is the first thing support will ask for.
- **`sendDocument` is not idempotent by default.** Pass an `Idempotency-Key`
  header when retrying.
- **Lifecycle transitions are not bookkeeping.** In several countries the
  transition *is* the receiver's statutory reporting obligation and is forwarded
  to the national platform. Do not fire them speculatively.
- **Multi-organization callers** must send `X-Flowie-Organization-Id`.

## Minimal end-to-end

```js
import { FlowieExchange } from "@flowie/exchange";

const flowie = await FlowieExchange.bootstrapSandbox();
const me = flowie.bootstrap.company.peppolId;

const doc = await flowie.sendDocument({
  type: "invoice",
  from: me,
  to: me,
  document: {
    number: "INV-001",
    issueDate: "2026-08-06",
    currency: "EUR",
    lines: [{ description: "Consulting", quantity: 1, unitPrice: 1000, vatRate: 20 }],
  },
});

await flowie.setLifecycle(doc.id, "approved");
const history = await flowie.getLifecycle(doc.id);
```

## Conventions when writing code against this API

- Read the OpenAPI contract rather than guessing a shape. 112 operations are
  documented; the named SDK methods cover the dozen that carry real traffic and
  `request()` reaches the rest.
- Treat country rules as data, not logic. They change constantly and are
  documented per country under <https://docs.get-flowie.com/compliance/> — do
  not hard-code a format or a deadline.
- Handle `429` by honouring `Retry-After`. Sandbox bootstrap is deliberately
  tight.
- Never log an API key. Keys are `flw_live_…`, `flw_test_…`, `flw_plat_…`,
  `flw_wl_…`.

## Machine-readable surfaces

| Surface | URL |
|---|---|
| Page index for agents | <https://docs.get-flowie.com/llms.txt> |
| Full corpus, one file | <https://docs.get-flowie.com/llms-full.txt> |
| OpenAPI 3.1 | <https://docs.get-flowie.com/openapi.json> |
| Markdown twin of any page | append `.md` to its URL |
| API catalog (RFC 9727) | <https://docs.get-flowie.com/.well-known/api-catalog> |
| Protected resource (RFC 9728) | <https://docs.get-flowie.com/.well-known/oauth-protected-resource> |
| Agent skills index | <https://docs.get-flowie.com/.well-known/agent-skills/index.json> |
| AI catalog (ARD) | <https://docs.get-flowie.com/.well-known/ai-catalog.json> |

## Support

Integration questions: support@flowie.fr (include the `X-Request-Id`).
Security disclosure: security@flowie.fr.
Status: <https://flowie.betteruptime.com/>
