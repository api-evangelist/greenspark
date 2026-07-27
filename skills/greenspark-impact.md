---
name: greenspark-impact
description: >-
  Guides Greenspark public impact ledger reads via MCP. Use when the user
  mentions impact purchases, impact ledger, getImpactPurchases, or
  getImpactPurchase.
---

# Greenspark Impact API (MCP reads)

## Related skills

Apply **greenspark-mcp** and **greenspark-auth-environments** before live calls.

- Domain reference: [https://docs.getgreenspark.com/reference/impacts](https://docs.getgreenspark.com/reference/impacts)

## Ledger endpoints (MCP when toggled)

| operationId | Route |
|-------------|-------|
| `getImpactPurchases` | GET `/impacts/purchases` |
| `getImpactPurchase` | GET `/impacts/purchases/:purchaseId` |

Use `get-endpoint` for schema, then optional `execute-request` on GET with a sandbox key.

Impact **creation** (POST) is documented on ReadMe but **not executable via MCP v1** — implement writes in your application and use the API docs for POST schemas.

## MCP rules (v1)

- Impact spec is **MCP-enabled** for GET ledger routes when toggled in ReadMe.
- Read-only via MCP in v1.

## Transparency (ledger responses)

Purchase responses include proof that impact was delivered, notably **`receiptUrl`** and an **`evidences`** array (impact-type-specific proof such as planting sessions or plastic collection details). Use `get-endpoint` for the full `TransactionPurchase` schema.

For how ledger fits into a full integration, see **greenspark-integration-workflow**.

## Agent guidance

- For ledger or purchase history: use `getImpactPurchases` or `getImpactPurchase`.
- Prefer sandbox for exploratory reads.
