---
name: deluxe-issue-an-invoice
description: Create, share, clone and download invoices on the Deluxe Payments Platform.
api: Deluxe Payments Platform (DPP) Invoice Experience API
generated: '2026-08-13'
method: generated
source: openapi/deluxe-dpp-invoices-openapi.yml, data-model/deluxe-data-model.yml
operations:
  - createDraftInvoice
  - modifyInvoiceDetails
  - modifyInvoiceStatus
  - getSpecificInvoice
  - searchInvoice
  - shareInvoice
  - cloneInvoice
  - downloadInvoice
  - createInvoiceConfiguration
  - getInvoiceConfiguration
  - modifyInvoiceConfiguration
---

# Issue an invoice on Deluxe

## 1. Configure once

`POST /invoices/configuration` (`createInvoiceConfiguration`) sets merchant-level invoice presentation
and numbering. `GET /invoices/configuration` (`getInvoiceConfiguration`) reads it back;
`PUT /invoices/configuration/{configurationId}` (`modifyInvoiceConfiguration`) changes it.

## 2. Draft

`POST /invoices` (`createDraftInvoice`). Keep the returned `invoiceId`.

## 3. Edit

Two different verbs, two different jobs — this is easy to get wrong:

- `PUT /invoices/{invoiceId}` (`modifyInvoiceDetails`) changes the **content** of the invoice.
- `PATCH /invoices/{invoiceId}` (`modifyInvoiceStatus`) changes its **status**.

## 4. Send

`POST /invoices/share` (`shareInvoice`) delivers it to the payer.

## 5. Read

- `GET /invoices/{invoiceId}` (`getSpecificInvoice`)
- `POST /invoices/search` (`searchInvoice`) — pagination is `pageNumber` / `pageSize` **in the request
  body**, not the query string.
- `GET /invoices/download/{invoiceId}` (`downloadInvoice`) returns the document.

## 6. Repeat business

`POST /invoices/clone` (`cloneInvoice`) copies an existing invoice as a new draft.

## Linking to payment

An invoice carries `customerId` and, once settled, `paymentId` — the same identifier space as the
Gateway API. Reconcile invoices against `searchPayments` on the Gateway API, not against the invoice
record alone.
