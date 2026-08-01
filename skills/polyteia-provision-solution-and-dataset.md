---
name: Provision a Polyteia solution and load a dataset
description: Create a workspace, create a solution inside it, and create a dataset in that solution via the Polyteia RPC API.
api: openapi/polyteia-platform-openapi.yml
operations: [create_workspace, create_solution, create_dataset]
---

# Provision a Polyteia solution and load a dataset

All calls are `POST https://app.polyteia.com/api` with headers
`Content-Type: application/json` and `Authorization: Bearer <PAK>` (Personal
Access Key). The body carries one `command` and its `params`. Responses return
HTTP 200 with either `data` (success) or `error` (`{code, message, details}`) —
always inspect the body, not just the status.

## Steps

1. **Create a workspace** — `command: create_workspace` with
   `params: { organization_id, name, description, attributes? }`. Capture
   `data.id` (a `ws_…` id).
2. **Create a solution** — `command: create_solution` with
   `params: { organization_id, workspace_id, name, description, attributes?, documentation? }`,
   using the `ws_…` id from step 1. Capture `data.id` (a `sol_…` id).
3. **Create a dataset** — `command: create_dataset` with
   `params: { name, solution_id, description, source, slug }`, using the `sol_…`
   id from step 2. `source` is a URL to the source file; `slug` must be unique
   within the solution. Capture `data.id` (a `ds_…` id).

## Rules

- There is no idempotency key; do not blindly retry a create on a network error —
  re-list to check whether the resource already exists before retrying.
- The PAK inherits the issuing account's permissions; a missing grant surfaces as
  `error.code: 403`.
- `slug` collisions and malformed params surface as `error.code: 400`.
