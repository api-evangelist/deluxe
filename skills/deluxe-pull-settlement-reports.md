---
name: deluxe-pull-settlement-reports
description: Pull Deluxe settlement, statement and transaction reports over a date range.
api: Deluxe Payments Platform (DPP) Reports Experience API
generated: '2026-08-13'
method: generated
source: openapi/deluxe-dpp-reports-openapi.yml, conventions/deluxe-conventions.yml
operations:
  - retrieveCreditCardDailySettlementReport
  - retrieveAchDailySettlementReport
  - retrieveCreditCardMonthlyFeesStatement
  - retrieveAchMonthlyFeesStatment
  - retrieveAuthorizedTransactions
  - retrieveCapturedTransactions
  - retrieveSettledTransactions
  - customReports
---

# Pull Deluxe reports

Base URL `https://api.deluxe.com/dpp/v1`. This API only supports `/dpp/v1` — not the
`/dpp/v1/gateway` alternate path.

## Reports available

| Operation | Path | What it returns |
|---|---|---|
| `retrieveCreditCardDailySettlementReport` | `GET /reports/ccdailysettlement` | Card settlement by day |
| `retrieveAchDailySettlementReport` | `GET /reports/achdailysettlement` | ACH settlement by day |
| `retrieveCreditCardMonthlyFeesStatement` | `GET /reports/ccmonthlystatement` | Card monthly fee statement |
| `retrieveAchMonthlyFeesStatment` | `GET /reports/achmonthlystatement` | ACH monthly fee statement |
| `retrieveAuthorizedTransactions` | `GET /reports/transauth` | Authorized transactions |
| `retrieveCapturedTransactions` | `GET /reports/transcaptured` | Captured transactions |
| `retrieveSettledTransactions` | `GET /reports/transsettled` | Settled transactions |
| `customReports` | `POST /reports` | A report by title and date range |

## Date format

`reportStartDate` and `reportEndDate` are **required** query parameters in **MM/DD/YYYY**, not ISO 8601:
`^(0[1-9]|1[0-2])/(0[1-9]|[12][0-9]|3[01])/[0-9]{4}$`. Example: `05/01/2025`.

## Pagination

`page` (min 1) and `pageSize` (min 1) query parameters. Deluxe publishes no maximum `pageSize`, no
total-count field and no cursor — page until a short page comes back.

> Note the inconsistency across the platform: reporting uses `page` in the query string, while invoice
> search (`searchInvoice`) uses `pageNumber` in the request body.

## Headers

`partnerToken` is required. `merchantNumber` is optional and scopes the report to one merchant.

## Rate limits

None are published, and no `RateLimit-*` or `Retry-After` header is documented. If you are backfilling
history, pace yourself and widen the date window rather than looping tightly.
