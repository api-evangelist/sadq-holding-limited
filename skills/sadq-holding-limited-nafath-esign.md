---
name: Sign a document with Nafath national identity
description: Apply a legally-binding digital signature using Saudi Arabia's Nafath national digital identity.
api: openapi/sadq-holding-limited-openapi-original.json
operations:
  - POST /api/v2/esign/nafath/sign
  - GET /api/v1/documents/{id}/signed
---

# Sign a document with Nafath national identity

Digitally sign a document where the signer authenticates through Nafath (Saudi national digital identity) using their national ID.

## Auth

Bearer JWT via OAuth 2.0 client_credentials (scope `Integrationscope`) — see authentication/sadq-holding-limited-authentication.yml.

## Steps

1. **Trigger a Nafath signature** — `POST /api/v2/esign/nafath/sign` (enhanced v2: biometric options, improved error handling, Nafath v3 auth flows). The signer approves the request in the Nafath app on their device. Use `POST /api/v1/esign/nafath/sign` for the v1 flow.
2. **Retrieve the signed file** — after approval, `GET /api/v1/documents/{id}/signed` to download the document with the embedded digital signature (PKI-backed).

## Rules

- Signing is legally binding once completed — confirm the correct document/signer before calling.
- The signer must complete Nafath approval out-of-band; poll or wait for confirmation before downloading.
- Handle the `{ errorCode, message }` envelope on `4xx`; `401` means the token is missing/expired.
