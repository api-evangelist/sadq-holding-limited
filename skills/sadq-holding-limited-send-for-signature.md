---
name: Send a document for signature
description: Upload a document to Sadq, invite a signer, and retrieve the completed signed file.
api: openapi/sadq-holding-limited-openapi-original.json
operations:
  - POST /api/v1/envelopes/initiate-base64
  - POST /api/v1/invitations
  - GET /api/v1/envelopes/{envelopeId}/status
  - GET /api/v1/envelopes/{envelopeId}/files/completed
---

# Send a document for signature

Use the Sadq Integration API to run a full e-signature flow: create an envelope from a document, invite a signer, poll for completion, and download the signed file.

## Auth

Obtain a Bearer JWT via OAuth 2.0 client_credentials (register at https://sadq.sa/register):

```
POST https://identity.sadq.sa/connect/token
grant_type=client_credentials&client_id=...&client_secret=...&scope=Integrationscope
```

Send `Authorization: Bearer {access_token}` on every request. Test against the sandbox host `https://sandbox-api.sadq-sa.com` before production `https://api.sadq.sa`.

## Steps

1. **Create the envelope** — `POST /api/v1/envelopes/initiate-base64` with the document content as a Base64 string. The response returns an `envelopeId` (and `documentId`). (Use `POST /api/v1/envelopes/initiate` for multipart file upload instead.)
2. **Invite the signer** — `POST /api/v1/invitations` referencing the envelope; the recipient gets an email/SMS link. Use `POST /api/v2/invitations` if you want the signing link returned in the response instead of Sadq sending notifications.
3. **Track status** — poll `GET /api/v1/envelopes/{envelopeId}/status` for signer completion and overall progress.
4. **Download the result** — once complete, `GET /api/v1/envelopes/{envelopeId}/files/completed` (or `GET /api/v1/documents/{id}/completed/base64`).

## Rules

- Errors come back as a `{ errorCode, message, ... }` envelope with the HTTP status — check both (see errors/sadq-holding-limited-problem-types.yml). A `409` with message "envelope is not complete" means you fetched the completed file too early.
- Cancellation (`POST /api/v1/envelopes/{envelopeId}/cancel`) is irreversible.
- No idempotency key is supported — do not blindly retry a `POST` that may have partially succeeded; re-query status first.
