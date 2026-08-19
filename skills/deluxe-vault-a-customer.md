---
name: deluxe-vault-a-customer
description: Store a customer and their payment instruments in the Deluxe vault, then bill them on a schedule.
api: Deluxe Payments Platform (DPP) Gateway Experience API
generated: '2026-08-13'
method: generated
source: openapi/deluxe-dpp-gateway-openapi.yml, data-model/deluxe-data-model.yml
operations:
  - createCustomer
  - getSpecificCustomer
  - getAllCustomers
  - modifySpecificCustomer
  - deleteSpecificCustomer
  - createPaymentMethod
  - modifyPaymentMethod
  - generateToken
  - getCustomerSPaymentMethod
  - deleteCustomerSPaymentMethod
  - createSubscription
  - modifySubscription
  - verifyAddress
  - verifyAch
---

# Vault a customer and bill them recurringly

## 1. Create the customer

`POST /customers` (`createCustomer`). Keep the returned `customerId`.

## 2. Vault an instrument

`POST /paymentmethods` (`createPaymentMethod`) against that `customerId`. Keep `paymentMethodId`.

Two verification helpers are available before you store:

- `POST /paymentmethods/avs` (`verifyAddress`) — address verification for cards.
- `POST /paymentmethods/verification/ach` (`verifyAch`) — validate a bank account before ACH origination.

## 3. Or tokenize instead

`POST /paymentmethods/token` (`generateToken`) returns a reusable `token`.

> A **token** and a **cryptogram** are different objects. A cryptogram comes from the Hosted Payment
> Form, is single-use, and expires in 15 minutes. A token is reusable. Deluxe warns explicitly not to
> confuse them. Note also that using `generateToken` directly puts you in PCI scope, because you handled
> the PAN; the Hosted Payment Form exists to avoid that (`components/deluxe-components.yml`).

## 4. Schedule recurring billing

`POST /subscriptions` (`createSubscription`) bound to the customer and payment method.
`PATCH /subscriptions/{subscriptionId}` (`modifySubscription`) to change it.

## 5. Read and clean up

- `GET /customers/{customerId}` (`getSpecificCustomer`) returns the profile plus its vaulted
  `vaults` and active `subscription` list.
- `GET /customers` (`getAllCustomers`) lists profiles.
- `DELETE /customers/{customerId}/paymentmethods/{paymentMethodId}`
  (`deleteCustomerSPaymentMethod`) removes one instrument.
- `DELETE /customers/{customerId}` (`deleteSpecificCustomer`) removes the profile.

## Identifier warning

Deluxe identifiers are bare GUIDs with no type prefix, and `customerId` is an integer in some responses
and a GUID in others. Never infer the resource type from the identifier — track it alongside.
