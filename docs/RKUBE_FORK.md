# RKube Fork Notes

This repository is tracked as the RKube/Rathna-K fork of `Casys-AI/mcp-erpnext`.
Do not push RKube-specific work to the upstream `Casys-AI` repository.

## Remotes

- `origin`: `https://github.com/Rathna-K/mcp-erpnext.git`
- `upstream`: `https://github.com/Casys-AI/mcp-erpnext`

Use `origin/main` for RKube deployments and submodule pointers in
`Rathna-K/frappe`. Use `upstream/main` only as the source for periodic syncs.

## RKube Custom Work

### Paged ERPNext List Reads

Commit: `Add paged list support for ERPNext tools`

Purpose:

- Avoid silent partial result sets when ERPNext list tools omit an explicit
  `limit`.
- Return pagination metadata so operators can see whether a response hit the
  safety cap.

Implementation:

- `FrappeClient.listPaged(...)` fetches pages until exhaustion or `maxRecords`.
- List responses include:
  - `fetched_count`
  - `truncated`
  - `max_cap_used`
- The following tools expose `auto_paginate` and `max_records` inputs:
  - `erpnext_account_list`
  - `erpnext_journal_entry_list`
  - `erpnext_payment_entry_list`
  - `erpnext_purchase_invoice_list`
  - `erpnext_sales_invoice_list`

Default behavior:

- If `limit` is omitted, tools auto-paginate up to `max_records` default `5000`.
- If `limit` is provided, tools preserve explicit bounded-list behavior.

## Sync Procedure

```bash
git fetch upstream main
git checkout main
git rebase upstream/main
deno test --allow-all tests/
git push origin main
```

If upstream touches the same list tools, preserve upstream viewer metadata such
as `DOCLIST_META` while keeping RKube pagination metadata in tool responses.

## Parent Repo

The parent `Rathna-K/frappe` repo should point the `mcp-erpnext` submodule at
the RKube fork URL, not the upstream Casys-AI URL.
