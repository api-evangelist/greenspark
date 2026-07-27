---
name: greenspark-reporting
description: >-
  Guides Greenspark Reporting API usage with fetchRawReportV2 as the canonical
  raw export. Use when the user mentions Greenspark reports, fetchRawReportV2,
  impact reports, spend reports, or report export.
---

# Greenspark Reporting API

## Related skills

Apply **greenspark-mcp** and **greenspark-auth-environments** before live calls.

## Canonical route

| operationId | Method | Route |
|-------------|--------|-------|
| `fetchRawReportV2` | GET | `/v2/reports` |

Use **`fetchRawReportV2`** for report export and MCP exploration.

The Reporting spec also includes aggregate and interval endpoints. Prefer **`fetchRawReportV2`** unless the user explicitly needs a specific aggregate — then use `get-endpoint` for that operation.

## MCP rules (v1)

- Reporting spec is **MCP-enabled** for GET routes when toggled in ReadMe.
- Read-only — no write operations in this spec.
- Use sandbox key for report exploration.

## Agent guidance

- Default answer for "how do I export reports?": `fetchRawReportV2` on GET `/v2/reports`.
