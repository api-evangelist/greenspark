---
name: greenspark-account
description: >-
  Guides Greenspark public account and community profile endpoints. Use when the
  user mentions Greenspark public account, widget data, getPublicAccountV2,
  community profile, or account comparisons by project.
---

# Greenspark Account API

## Related skills

Apply **greenspark-mcp** and **greenspark-auth-environments** before live calls.

## Endpoints

| operationId | Purpose |
|-------------|---------|
| `getPublicAccountV2` | Public account — impacts and comparisons by project |
| `getCommunityPublicAccount` | Greenspark community public profile |

## Use cases

- **Merchant widget / public embed:** `getPublicAccountV2` with account id
- **Community pages:** `getCommunityPublicAccount`
- **Schema discovery:** `get-endpoint` before building frontend or backend proxy

## MCP rules (v1)

- Account spec is **MCP-enabled** for GET routes when toggled in ReadMe.
- Read-only via MCP in v1.
- Match sandbox key to sandbox host for testing.

## Agent guidance

- When user asks for "account impacts" or "comparisons by project": use `getPublicAccountV2`.
