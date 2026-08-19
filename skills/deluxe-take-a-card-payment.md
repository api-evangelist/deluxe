---
name: deluxe-take-a-card-payment
description: Run a card sale through the Deluxe Payments Platform, then void or refund it.
api: Deluxe Payments Platform (DPP) Gateway Experience API
generated: '2026-08-13'
method: generated
source: openapi/deluxe-dpp-gateway-openapi.yml, sandbox/deluxe-sandbox.yml, errors/deluxe-decline-codes.yml
operations:
  - createPayment
  - authorizePayment
  - completePayment
  - cancelPayment
  - createRefund
  - searchPayments
  - closeBatch
---

# Take a card payment on Deluxe

Base URL `https://api.deluxe.com/dpp/v1` (sandbox `https://sandbox.api.deluxe.com/dpp/v1`).
Authenticate first — see `deluxe-authenticate`.

## Immediate sale

`POST /payments` (`createPayment`) with `paymentType: "Sale"`, an `amount` object
(`{amount, currency}` where currency is `USD` or `CAD`), and a `paymentMethod`. `paymentMethod` is a
**union** — supply exactly one of `card`, `ach`, `token`, `vault`, `cryptogram` or `networkToken`.

Read the result from the response body, not the HTTP status:

- `responseCode == 0` → approved. Keep `paymentId`, `authResponse` and `batchNumber`.
- `responseCode != 0` → declined. Look the code up in `errors/deluxe-decline-codes.yml`.

## Authorize then capture

1. `POST /payments/authorize` (`authorizePayment`) to reserve funds.
2. `POST /payments/complete` (`completePayment`) with the returned `paymentId` to capture.

## Void vs refund

- Before settlement: `POST /payments/cancel` (`cancelPayment`).
- After settlement: `POST /refunds` (`createRefund`), referencing `parentPaymentId`.

## Settle

`POST /batches` (`closeBatch`) closes the open settlement batch.

## Interchange optimization

For B2B and government cards, populate `level2` (customer ref number, tax amount, shipping zip) and
`level3` (per-line commodity code, unit of measure, unit cost, discount, tax, freight, duty). These are
first-class fields on `createPayment` and reduce interchange.

## Critical safety rule

**Deluxe publishes no idempotency mechanism.** There is no idempotency header on `createPayment`,
`createRefund` or any batch operation. A retried POST can charge the cardholder twice. Before retrying:

1. Never auto-retry a 5xx or a timeout on a money-moving call.
2. Instead, call `POST /payments/search` (`searchPayments`) with your own `orderId` to find out whether
   the first attempt landed.
3. Always send your own `orderId` so that search is possible.

## Testing

Sandbox test cards, trigger cards and test bank details are in `sandbox/deluxe-sandbox.yml`. Expiry can
be any future date; auth code `123456` is returned for approvals.
