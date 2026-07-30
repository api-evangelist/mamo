---
name: Pay out to a recipient
description: Create a payout recipient and issue a disbursement, then confirm settlement.
api: openapi/mamo-openapi.yml
operations: [postAccountsRecipients, postDisbursements, getDisbursementsDisbursementid]
---

# Pay out to a recipient

Send funds to a third party via Mamo payouts.

## Auth
`Authorization: Bearer <api_key>`. Base URL `https://business.mamopay.com/manage_api/v1`.

## Steps
1. **Create the recipient** — `postAccountsRecipients` (`POST /accounts/recipients`) with the
   recipient's payout details. The response returns a recipient identifier.
2. **Issue the disbursement** — `postDisbursements` (`POST /disbursements`). Disbursements can
   be issued in bulk; reference the recipient identifier(s) from step 1.
3. **Confirm settlement** — `getDisbursementsDisbursementid`
   (`GET /disbursements/{disbursementId}`), and/or subscribe to the `payout.processed` and
   `payout.failed` webhook events.

## Rules
- Check the business balance first with `getFinances` (`GET /finances`).
- A rejected payout raises `payout.failed`; inspect the reason before retrying.
