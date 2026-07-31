---
name: Fulfill a Mirakl marketplace order
description: As a seller, retrieve new orders, accept order lines, add carrier tracking, and validate shipment on a Mirakl-powered marketplace.
api: openapi/mirakl-mmp-seller-openapi.json
operations: [OR11, OR21, OR23, OR24]
auth: Shop-API-Key (Authorization header)
---

# Fulfill a Mirakl marketplace order (seller)

Base URL is your operator's instance, `https://<instance>.mirakl.net`. Authenticate every
call with your shop API key in the `Authorization` header. If your user is linked to
multiple shops, pass `shop_id` to select the shop.

## Steps

1. **List orders to process** — `OR11` `GET /api/orders`.
   Page with `offset`/`max`; filter with `order_state_codes` (e.g. `WAITING_ACCEPTANCE`)
   and `updated_since` for incremental sync. Tolerate new fields and new enum values.
2. **Accept (or refuse) the order lines** — `OR21` `PUT /api/orders/{order_id}/accept`.
   Provide the per-line accept/refuse decisions.
3. **Add carrier tracking** — `OR23` `PUT /api/orders/{order_id}/tracking`.
   Supply carrier code/name and tracking number.
4. **Validate the shipment** — `OR24` `PUT /api/orders/{order_id}/ship`.
   This transitions the order to shipped.

## Rules

- No request idempotency key is documented — make each write once and reconcile by
  re-reading the order (`OR12 GET /api/orders/{order_id}`) rather than blindly retrying.
- Errors return a JSON body (typically `400`); see `errors/mirakl-problem-types.yml`.
- For high volume, prefer the async export (`OR13`/`OR14`/`OR15`) over paging `OR11`.
