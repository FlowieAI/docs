---
name: einvoicing-country-rules
description: Look up e-invoicing mandate rules, required formats, networks and deadlines for a specific country (47 covered, including France PA/PPF, Italy SDI, Saudi Fatoora, India GST IRP, Malaysia MyInvois). Use when asked whether or how e-invoicing applies somewhere.
---

# Country e-invoicing rules

## When to use this

Someone asks whether e-invoicing is mandatory somewhere, what format is
required, which network to use, or when a deadline lands. Rules change
constantly — **look them up, do not recall them from memory and do not
hard-code them**.

## Per-country documentation

```
https://docs.get-flowie.com/compliance/<iso2>        e.g. /compliance/it
https://docs.get-flowie.com/compliance/<iso2>.md     markdown, for agents
https://docs.get-flowie.com/compliance/fr/           France, in depth
```

Each page carries the mandate status, the clearance model, required formats,
the network, and the effective dates.

## Live compliance posture for a company

```bash
curl "https://back.flowie.ink/exchange/v1/compliance/status?country=FR"   -H "Authorization: Bearer $KEY"
```

## Via MCP

The documentation is queryable over MCP with no credentials:

```
https://docs.get-flowie.com/mcp     tools: docs_search, docs_read_page
```

## Notes

- Coverage spans 47 countries; Peppol interoperates across most of Europe with
  national profiles layered on top.
- France requires transmission through a registered *Plateforme Agréée*; Flowie
  is one.

## Reference

https://docs.get-flowie.com/compliance/ · https://docs.get-flowie.com/llms.txt
