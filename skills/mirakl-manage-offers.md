---
name: Manage Mirakl shop offers
description: As a seller, list offers, create/update/delete offers, import an offer file, and check available stock on a Mirakl marketplace.
api: openapi/mirakl-mmp-seller-openapi.json
operations: [OF21, OF24, OF01, OF26]
auth: Shop-API-Key (Authorization header)
---

# Manage Mirakl shop offers (seller)

Base URL is your operator's instance, `https://<instance>.mirakl.net`. Authenticate with
the shop API key in the `Authorization` header; pass `shop_id` when multi-shop.

## Steps

1. **List current offers** — `OF21` `GET /api/offers`. Page with `offset`/`max`.
2. **Create, update, or delete offers (synchronous)** — `OF24` `POST /api/offers`.
   Send the offer payload with the operation per offer.
3. **Bulk import offers (asynchronous)** — `OF01` `POST /api/offers/imports`.
   Upload a CSV/JSON file, capture the returned `import_id`, then poll `OF02`
   `GET /api/offers/imports/{import}` and pull the error report via `OF03` if needed.
4. **Check available stock for an offer** — `OF26` `GET /api/offers/{offer}/quantity`.

## Rules

- Large catalogs go through the async import (`OF01`) not the synchronous `OF24`.
- No idempotency-key header — dedupe on your side and re-read to confirm.
- New enumeration values (including offer states, `OF61`) may appear over time.
- Error envelope: see `errors/mirakl-problem-types.yml`; conventions in
  `conventions/mirakl-conventions.yml`.
