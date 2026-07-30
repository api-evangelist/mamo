---
name: Create and track a Mamo payment link
description: Generate a hosted payment link, then confirm the resulting payment via the transaction API or a webhook.
api: openapi/mamo-openapi.yml
operations: [postLinks, getLinksLinkid, getChargesChargeid, postWebhooks]
---

# Create and track a Mamo payment link

Use the Mamo Business API to generate a hosted payment link and confirm payment.

## Auth
Send `Authorization: Bearer <api_key>` on every request. Get the key from the Mamo
dashboard (Developer > Keys). Use the sandbox base URL
`https://sandbox.dev.business.mamopay.com/manage_api/v1` while testing, then switch to
`https://business.mamopay.com/manage_api/v1`.

## Steps
1. **Create the link** — `postLinks` (`POST /links`). Provide the amount, currency and
   title. Optionally set `external_id` to correlate the link with your own records and
   `custom_data` for arbitrary metadata. The response returns the payment link (id prefix
   `MB-LINK-`) and its hosted URL.
2. **Present the link** to the customer. On completion Mamo redirects to your `return_url`
   with `paymentLinkId`, `transactionId`, `status` and `createdAt` query params.
3. **Confirm the payment** — either:
   - poll `getChargesChargeid` (`GET /charges/{chargeId}`) with the `transactionId`, or
   - register a webhook once with `postWebhooks` (`POST /webhooks`) subscribing to
     `charge.succeeded` and `charge.failed`, and react to the delivered event.

## Rules
- Test with the published sandbox cards (see `sandbox/mamo-sandbox.yml`); CVV `123`, expiry
  `01/28`, 3DS password `Checkout1!`.
- There is no server-side Idempotency-Key; use `external_id` to detect duplicates on your side.
- On decline, map the returned `error_code` using `errors/mamo-decline-codes.yml`.
