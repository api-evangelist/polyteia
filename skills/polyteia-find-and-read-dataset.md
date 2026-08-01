---
name: Find and read a Polyteia dataset
description: Navigate the organization/workspace/solution hierarchy to locate and read a dataset via the Polyteia RPC API.
api: openapi/polyteia-platform-openapi.yml
operations: [list_organizations, list_workspaces, list_solutions, list_datasets, get_dataset]
---

# Find and read a Polyteia dataset

All calls are `POST https://app.polyteia.com/api` with
`Authorization: Bearer <PAK>` and `Content-Type: application/json`. Reads use a
`query` field. List queries return a paginated envelope
`data: { total, page, size, items }`.

## Steps

1. **List organizations** — `query: list_organizations` with
   `params: { page, size }`. Pick the target `org_…` id.
2. **List workspaces** — `query: list_workspaces` with `params: { page, size }`;
   filter `items` by `organization_id`. Pick the `ws_…` id.
3. **List solutions** — `query: list_solutions` with `params: { page, size }`;
   filter by `workspace_id`. Pick the `sol_…` id.
4. **List datasets** — `query: list_datasets` with
   `params: { page, size, solution_id }`. Find the target dataset.
5. **Read the dataset** — `query: get_dataset` with either
   `params: { id }` (a `ds_…` id) or `params: { solution_id, slug }`. The
   response includes `metadata.schema.columns` (column types) and
   `metadata.asset_info` (content_type, size, storage_backend).

## Rules

- Paginate with `page`/`size` until `page * size >= total`.
- A missing/unknown id returns `error.code: 404`; an unpermitted read returns `403`.
- `get_dataset` by `slug` requires the owning `solution_id` — slugs are only
  unique within a solution.
