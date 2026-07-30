---
name: Charge a saved card and refund
description: Initiate a merchant-initiated charge against a previously saved card, then refund it.
api: openapi/mamo-openapi.yml
operations: [postCharges, getChargesChargeid, postChargesChargeidRefunds]
---

# Charge a saved card and refund

Run a Merchant Initiated Transaction (MIT) against a card saved on a prior payment, then
refund it if needed.

## Auth
`Authorization: Bearer <api_key>`. Base URL `https://business.mamopay.com/manage_api/v1`
(sandbox: `https://sandbox.dev.business.mamopay.com/manage_api/v1`).

## Prerequisites
A card id from a previous transaction (the customer paid once via a payment link created
with card-saving enabled; the card id is on the charge details / webhook).

## Steps
1. **Initiate the charge** — `postCharges` (`POST /charges`) with the saved card id and the
   amount/currency. This performs the MIT.
2. **Verify status** — `getChargesChargeid` (`GET /charges/{chargeId}`) using the returned
   charge id (prefix `MPB-CHRG-`). Confirm `status` is captured.
3. **Refund if required** — `postChargesChargeidRefunds`
   (`POST /charges/{chargeId}/refunds`). Track the refund with the
   `charge.refund_initiated` / `charge.refunded` / `charge.refund_failed` webhook events.

## Rules
- A failed charge returns a normalized `error_code` — resolve it via
  `errors/mamo-decline-codes.yml` before retrying.
- No Idempotency-Key header exists; guard against double-charging on your side (e.g. dedupe
  on `external_id`).
