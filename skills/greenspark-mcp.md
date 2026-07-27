---
name: greenspark-mcp
description: >-
  Connects to the Greenspark ReadMe MCP server, lists API specs and endpoints,
  and performs read-only GET execution. Use when integrating Greenspark API via
  MCP, Cursor, Claude Desktop, or when the user mentions Greenspark MCP,
  execute-request, list-endpoints, or docs.getgreenspark.com/mcp.
---

# Greenspark MCP (v1)

## Prerequisites

Before live API calls, apply the **greenspark-auth-environments** skill for key and host rules.

## MCP server

- **URL:** `https://docs.getgreenspark.com/mcp`
- **Hosted by:** ReadMe (this repo does not implement the server)
- **Password:** none for v1
- **Docs:** [https://docs.getgreenspark.com](https://docs.getgreenspark.com)
- **llms.txt:** [https://docs.getgreenspark.com/llms.txt](https://docs.getgreenspark.com/llms.txt)

## Built-in tools (ReadMe)

Schemas and routes come from OpenAPI **uploaded to ReadMe** — not from open-api `-json` export URLs. Use MCP tools below; do not expect `GET /v1/api/*-json` via MCP.

| Tool | Purpose |
|------|---------|
| `list-specs` | List OpenAPI specs exposed to MCP |
| `list-endpoints` | List routes in a spec |
| `get-endpoint` | Full operation schema (params, responses) |
| `search-endpoints` | Find routes by keyword |
| `execute-request` | Live HTTP call — **GET only in v1**, on enabled routes |
| `get-server-variables` | Environment / server URL hints from spec |

## v1 read-only contract

- **Do not** use `execute-request` for POST, PUT, PATCH, or DELETE.
- For write operations: use `get-endpoint` to read the schema, then generate application code that calls the Greenspark API directly.
- `execute-request` only works on GET routes **enabled in ReadMe MCP route toggles**.

## Spec exposure (v1)

| Spec | MCP live GET | Notes |
|------|--------------|-------|
| Impact | Yes | Ledger GET — see **greenspark-impact** |
| Reporting | Yes | Prefer `fetchRawReportV2` — see **greenspark-reporting** |
| Account | Yes | Prefer `getPublicAccountV2` — see **greenspark-account** |
| Projects | Yes | See **greenspark-projects** |
| Estimations | Docs only | `get-endpoint` only; not in MCP route toggles |
| Email | Docs only | `get-endpoint` only; not in MCP route toggles |
| Billing | Docs only | Not in MCP route toggles |

## Recommended workflow

1. `list-specs` — confirm target spec is MCP-enabled or docs-only.
2. `list-endpoints` or `search-endpoints` — find the operation.
3. `get-endpoint` — read parameters, auth, and response shape.
4. `execute-request` — optional; GET only, sandbox key recommended.

## Smoke-test prompts (read-only)

- "List Greenspark MCP endpoints for the Account spec."
- "Show the schema for operationId getPublicAccountV2."
- "Search endpoints for impact purchases ledger."

## Domain skills

Use alongside:

- **greenspark-integration-workflow** — how domains connect; typical integration flow (start here when building)
- **greenspark-auth-environments** — keys, hosts, 401/403/422
- **greenspark-impact** — impact purchase ledger GET
- **greenspark-reporting** — raw reports (`fetchRawReportV2`)
- **greenspark-account** — public account (`getPublicAccountV2`)
- **greenspark-projects** — project catalog

## Client config

See [examples/cursor-mcp.json](https://github.com/getgreenspark/mcp/blob/master/examples/cursor-mcp.json). Prefer sandbox `x-api-key` for testing.
