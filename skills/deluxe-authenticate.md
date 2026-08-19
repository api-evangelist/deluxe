---
name: deluxe-authenticate
description: Obtain and manage a Deluxe Payments Platform bearer token before calling any DPP operation.
api: Deluxe Payments Platform (DPP)
generated: '2026-08-13'
method: generated
source: >-
  https://docs.deluxe.com/docs/deluxe-payments-platform/zoi9qoo2d5tf2-deluxe-payments-platform,
  raml/deluxe-security-schemes.raml.json, openapi/deluxe-postman-sandbox-openapi.yml
operations: []
---

# Authenticate against the Deluxe Payments Platform

Every DPP operation is secured by `oidcEnforcement` — an OAuth 2.0 bearer token in the
`Authorization` header. There is no API-key path.

## Before you start

You need a **Client Application**: a Client ID and Client Secret issued by Deluxe. There is no
self-serve signup. Request one by emailing `isvinquiries@deluxe.com`.

## Steps

1. **Request a token.** POST to the security service token endpoint with
   `Content-Type: application/x-www-form-urlencoded`, sending the Client ID and Client Secret.
   Sandbox: `https://sandbox.api.deluxe.com/secservices/oauth2/v2/token`.
   The production token host is issued with your credentials and is not published publicly.
2. **Cache the token.** It is valid for **60 minutes**. Deluxe recommends refreshing at **45 minutes**.
3. **Do not re-authenticate per call.** "If the Bearer token has not expired, the server will not
   return a new bearer token" — you will be handed the same one back.
4. **Send it on every request** as `Authorization: Bearer <token>`.
5. **Send the merchant header.** `partnerToken` is a **required** GUID header on every operation
   (`^[0-9a-fA-F]{8}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{4}-[0-9a-fA-F]{12}$`). Reporting
   operations may also take `merchantNumber`.
6. **Send a correlation id.** `requestId` is an optional GUID header that Deluxe "strongly recommends"
   on every request; it is echoed back in the response body. It is **not** an idempotency key — see
   `conventions/deluxe-conventions.yml`.

## Failure handling

| Status | Meaning | Do |
|---|---|---|
| 401 | Invalid token, expired token, or the authorization server could not be reached | Re-authenticate once, then retry |
| 403 | Invalid client application credentials | Stop. Check the Client ID/Secret and the `partnerToken` |

Business failures do **not** arrive as 4xx. A declined payment returns **HTTP 200** with a non-zero
`responseCode`. See `errors/deluxe-decline-codes.yml`.
