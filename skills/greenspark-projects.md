---
name: greenspark-projects
description: >-
  Guides Greenspark impact project catalog browsing: categories, project lists,
  and project detail with plan requirements. Use when the user mentions Greenspark
  projects, getProjects, getProject, project categories, or impact project catalog.
---

# Greenspark Projects API

## Related skills

Apply **greenspark-mcp** and **greenspark-auth-environments** before live calls.

## Operations

| operationId | Purpose | MCP notes |
|-------------|---------|-----------|
| `getProjectCategories` | List impact project categories | GET; MCP when toggled |
| `getProjects` | List impact projects for account | GET; MCP when toggled |
| `getProject` | Project detail | GET; **premium plan** may be required |
| `getProjectPage` | Project page content | GET; supplementary |
| `getProjectMetadata` | Project metadata | GET; supplementary |
| `getProjectStories` | Project stories | GET; supplementary |

## MCP rules (v1)

- Projects spec is **MCP-enabled** for GET routes when toggled in ReadMe.
- Read-only via MCP in v1.
- Typical flow: `getProjectCategories` → `getProjects` (use query filters). Fetch by id via `getProject` only when you already have a `projectId`.

## Agent guidance

- For "browse available projects": start with categories and `getProjects`; use **filters** on `getProjects` to narrow the catalog.
- List and single-project responses share the same project shape — prefer list + filters for discovery.
- Use sandbox key for catalog exploration.
- Integration sequencing: **greenspark-integration-workflow**.
