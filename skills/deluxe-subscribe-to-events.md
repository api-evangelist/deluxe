---
name: deluxe-subscribe-to-events
description: Subscribe an HTTPS listener to Deluxe Payments Platform webhooks and verify delivery.
api: Deluxe Payments Platform (DPP) Gateway Experience API
generated: '2026-08-13'
method: generated
source: openapi/deluxe-dpp-gateway-openapi.yml, asyncapi/deluxe-webhooks.yml
operations:
  - subscribeEvent
  - unsubscribeEvent
  - performTestEvent
  - resendEvent
  - retrieveEventsReports
---

# Subscribe to Deluxe webhooks

## 1. Choose the scope carefully

`POST /events/subscribe` (`subscribeEvent`) takes a `userName` and an `events[]` array of
`{eventUri, eventType}`.

> **Scope trap.** "Submitting Username will result in a subscription for **all accounts available under
> that user's portfolio**. Submitting Access Token will result in a subscription for **that merchant
> account only**." For an ISV with many merchants, the wrong choice here fans every merchant's events at
> one listener.

## 2. Event types

Exactly eight are published:

`MERCHANT BOARDED`, `MERCHANT UPDATED`, `CC BATCH`, `ACH BATCH`, `ACH REJECT`, `TRANSACTION`,
`VAULT`, `CC CHARGEBACK`.

## 3. Listener requirements

`eventUri` must be HTTPS — the contract enforces
`^https:\/\/([a-zA-Z0-9-]+\.)+[a-zA-Z]{2,}(\/[a-zA-Z0-9\-_]+)*(\/)?$`.

## 4. Verify

`POST /events/performTest` (`performTestEvent`) fires a test event at your listener.
`POST /events/report` (`retrieveEventsReports`) returns delivery activity.
`POST /events/resend` (`resendEvent`) re-delivers an event you missed.

## 5. Unsubscribe

`POST /events/unsubscribe` (`unsubscribeEvent`).

## What Deluxe does NOT publish

Build defensively — none of the following exists in the public contract:

- **No payload schema for any of the eight event types.** You cannot know the body shape until you
  receive one. Log the raw body on first receipt.
- **No signature, HMAC or shared secret.** There is no documented way to verify a delivery came from
  Deluxe. Treat the listener as unauthenticated: re-fetch state from the API (`searchPayments`,
  `getSpecificCustomer`) rather than trusting the webhook body.
- **No retry or delivery-guarantee policy.** Assume at-most-once and reconcile with
  `retrieveEventsReports`.
- **No AsyncAPI document.**
