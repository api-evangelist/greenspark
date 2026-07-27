---
name: greenspark-auth-environments
description: >-
  Configures Greenspark API authentication and environment selection using
  x-api-key and matching API hosts. Use when the user asks about Greenspark API
  keys, sandbox vs production, 401/403/422 errors, or before
  any live Greenspark API or MCP execute-request call.
---

# Greenspark auth and environments

## API key header

All Greenspark API calls require:

```
x-api-key: <your-api-key>
```

Keys are created in the **Greenspark dashboard** after registration. Use a **separate key per environment**.

## Host and key pairing (required)

| Environment | API host | Key type |
|-------------|----------|----------|
| Production | `https://api.getgreenspark.com` | Production API key |
| Sandbox | `https://sandbox.getgreenspark.com` | Sandbox API key |

**Never mix** — a sandbox key against production (or vice versa) will fail.

Default to **sandbox** for exploration, MCP smoke tests, and agent-driven trials.

## Key types

Documentation states: **Accepts all standard API key types** — individual routes may restrict allowed types. Check `get-endpoint` for route-specific requirements.

## MCP client config

See [examples/cursor-mcp.json](https://github.com/getgreenspark/mcp/blob/master/examples/cursor-mcp.json). Prefer a sandbox `x-api-key` for testing.

If your client does not support headers on the MCP URL, pass `x-api-key` when invoking `execute-request` against Greenspark API hosts.

## HTTP error map

| Status | Meaning | Action |
|--------|---------|--------|
| **401** | Missing or invalid `x-api-key` | Verify key exists and header is set |
| **403** | Plan or permission denied | Check subscription tier for the route |
| **422** | API quota exhausted | Codes include `MAX_API_CALLS_REACHED`, `MAX_API_CALLS_REACHED_FOR_MERCHANT` — do not retry blindly |

### Common 403 plan tiers (from API docs)

- **Business:** `growthBusiness`, `growthBusinessYearly`, `premiumBusiness`, `premiumBusinessYearly`, `enterpriseBusiness` (route-dependent)
- **Premium:** `premiumBusiness`, `premiumBusinessYearly`, `enterpriseBusiness` (route-dependent)
- Upgrade messaging in responses: growth, premium, or enterprise plan required

## Security rules

- Never log, paste, or commit API keys.
- Never use production keys for automated agent experiments without explicit user approval.
- Warn before POST routes that **bill the account** (especially on production).

## Related skills

- **greenspark-mcp** — MCP tools and read-only v1 rules
- Domain skills (impact, reporting, account, projects) — route-specific plan gates
