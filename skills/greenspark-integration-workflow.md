---
name: greenspark-integration-workflow
description: >-
  Explains how Greenspark API domains connect and walks through a typical
  external integration: project discovery, impact creation, end-customer
  visibility, business rollup, and transparency via the impact ledger. Use when
  planning or building a Greenspark integration, onboarding via MCP, or when the
  user asks how Impact, Projects, Account, and Reporting fit together.
---

# Greenspark integration workflow

## Prerequisites

Apply before this skill:

- **greenspark-auth-environments** — `x-api-key`, sandbox vs production hosts
- **greenspark-mcp** — MCP tools, v1 read-only contract

Use domain skills for endpoint detail; this skill is the **integration story** only.

## Personas

| Persona | Who | Role in the flow |
|---------|-----|------------------|
| **Greenspark** | Platform | Hosts API, docs, and MCP |
| **External developer** | Business customer of Greenspark | Builds the integration; owns API keys and dashboard setup |
| **End customer** | Customer of the business customer (B2B2C) | Performs actions (purchase, signup, form fill, etc.) that should create or surface impact |

## How domain skills relate

| Domain skill | Role in integration |
|--------------|---------------------|
| **greenspark-projects** | Discover which impact **projects** to fund (`projectId` in create payloads) |
| **greenspark-impact** | **Create** impacts (app code) and **prove** them via the public ledger GET |
| **greenspark-account** | **Business-customer** rollup display (`getPublicAccountV2`) |
| **greenspark-reporting** | **End-customer** detailed impact rows (`fetchRawReportV2`, metadata filters) |

Impact **creation** (POST) is not executable via MCP v1. Use MCP to read schemas (`get-endpoint`) and validate GET paths; implement writes in your application ([Impact API](https://docs.getgreenspark.com/reference/impacts.md)).

## Domain map

```text
Auth → Projects (discover projectIds)
         ↓
    Impact POST (createImpact or createTailoredImpact + metadata)
         ↓
    ┌────┴────┬──────────────────┐
    ▼         ▼                  ▼
 Sync UI   Reporting v2      Public Account V2
 (response) (metadata filter)  (business rollup)
    │
    └──► Impact ledger GET (transparency: receipt + evidences)
```

## Impact creation (application code)

Choose one primary write path:

### Source + trigger (`createImpact`)

`POST /impacts/sources/:sourceId/triggers/:triggerId`

- **When:** Many impacts share one **source** (tool, API, channel) and **triggers** (customer actions).
- **Setup:** Create a **custom integration** in the Greenspark dashboard to obtain `sourceId` and `triggerId`.
- **Segmentation:** Filter reporting by `sourceId` / `triggerId` as well as metadata.

### One-time impact (`createTailoredImpact`)

[Create One-time Impact](https://docs.getgreenspark.com/reference/createtailoredimpact.md) — `POST /impacts`

- **When:** Simpler flows without dashboard integration setup.
- **No** `sourceId` / `triggerId` on the route.

### Metadata (both paths)

Optional `metadata` array (key-value pairs) on any impact creation body. Use it to attach business data (e.g. order id, end-customer id, session id). The same keys can **filter** rows in [fetchRawReportV2](https://docs.getgreenspark.com/reference/fetchrawreportv2.md). See [impact segmentation](https://docs.getgreenspark.com/reference/data-segmentation.md).

Store `purchaseId` or correlation keys from the create response when linking to ledger or reporting later.

## Typical integration flow

### 1. Authenticate

- Create API keys in the Greenspark dashboard ([environments](https://docs.getgreenspark.com/reference/environments.md)).
- Match **sandbox key** → `https://sandbox.getgreenspark.com`; **production key** → `https://api.getgreenspark.com`.
- Default to sandbox for development and MCP trials.

### 2. Discover projects

- Optional: `getProjectCategories` when the UX is category-driven.
- **`getProjects`** — primary catalog call; use query **filters** to narrow projects (see **greenspark-projects** and `get-endpoint` for filter fields).
- List and detail share the same project shape; prefer list + filters unless a single id is already known.

### 3. Place impact creation in your flows

On end-customer actions, call the chosen Impact POST from your backend with:

- `impactPurchases` referencing selected `projectId` values
- `metadata` when you need per-end-customer correlation or reporting filters
- `sourceId` / `triggerId` only when using `createImpact`

Handle **401**, **403** (plan), and **422** (quota) per **greenspark-auth-environments**. POST routes **bill** the account — use sandbox until production is intentional.

### 4. Visibility

Three audiences; combine as needed.

| Audience | Goal | Approach |
|----------|------|----------|
| **End customer (immediate)** | Show proof right after the action | Return relevant fields from the **create response** (and/or your metadata) in the same user journey |
| **End customer (async)** | Detailed per-action impact history | **`fetchRawReportV2`** with **metadata** (and source/trigger filters if used). Prefer v2 raw export; use aggregate report endpoints only when the user explicitly wants charts or summaries (**greenspark-reporting**) |
| **Business customer** | High-level totals and impact equivalents | **`getPublicAccountV2`** — `publicAccountId` from the Greenspark dashboard (**greenspark-account**) |

Reporting rows are suited to **end-customer** action context; public account is suited to the **merchant / business customer** overview.

### 5. Transparency (impact ledger)

After creation, the public ledger proves Greenspark fulfilled the purchase.

| operationId | Use |
|-------------|-----|
| `getImpactPurchase` | Single purchase by `purchaseId` from the create response |
| `getImpactPurchases` | Browse or audit the ledger |

Response highlights (see `get-endpoint` / [Impacts reference](https://docs.getgreenspark.com/reference/impacts.md)):

- **`receiptUrl`** — receipt for the purchase
- **`evidences`** — proof of on-the-ground impact (e.g. planting sessions, plastic collection metadata such as images and coordinates, depending on impact type)

Use the ledger for trust UI, support, and debugging — especially when async reporting is not yet available. MCP v1 can call these GETs when enabled (**greenspark-impact**).

## MCP validation checklist (read-only)

Before generating integration code:

1. Confirm auth and sandbox host (**greenspark-auth-environments**).
2. `list-specs` — Impact, Reporting, Account, Projects are MCP-live.
3. `execute-request` (optional): `getProjects` with representative filters.
4. `execute-request` (optional): `getPublicAccountV2` with dashboard `publicAccountId`.
5. `execute-request` (optional): narrow `fetchRawReportV2` sample.
6. `execute-request` (optional): `getImpactPurchase` for a known `purchaseId`.
7. `get-endpoint` for your chosen Impact POST (`createImpact` or `createTailoredImpact`) — implement writes in app code only.

## Plan and quota gates

Route-level **403** and **422** depend on subscription and quota. Check `get-endpoint` and live responses; do not retry blindly on 422.

## Out of typical scope

- **getCommunityPublicAccount** — Greenspark-wide community totals; not part of a standard merchant integration.
- **Estimations, Email, Billing** — other OpenAPI specs; not covered by this workflow.
- **Widget** reference endpoints in docs — separate from this open-API integration path unless explicitly required.

## Related skills

| Skill | Open when |
|-------|-----------|
| **greenspark-auth-environments** | Keys, hosts, errors |
| **greenspark-mcp** | MCP tools and v1 limits |
| **greenspark-projects** | Catalog and filters |
| **greenspark-impact** | Ledger GET and Impact POST reference |
| **greenspark-account** | `getPublicAccountV2` |
| **greenspark-reporting** | `fetchRawReportV2` and aggregates |

Docs index: [llms.txt](https://docs.getgreenspark.com/llms.txt) · MCP guide: [docs/mcp](https://docs.getgreenspark.com/docs/mcp)
