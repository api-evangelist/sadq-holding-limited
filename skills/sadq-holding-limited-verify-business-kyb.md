---
name: Verify a Saudi business before signing (KYB)
description: Run Know-Your-Business checks — commercial registration, Absher OTP, and power-of-attorney delegacy — before letting someone sign on a company's behalf.
api: openapi/sadq-holding-limited-openapi-original.json
operations:
  - GET /api/v2/kyb/check-cr/{commercialNumber}
  - GET /api/v1/kyb/absher-otp/{commercialNumber}/{nationalId}
  - GET /api/v1/kyb/delegacy/{nationalId}/{delegatedReferenceId}
---

# Verify a Saudi business before signing (KYB)

Confirm a business and that an individual is authorized to sign on its behalf, using Sadq's KYB group backed by Saudi national rails.

## Auth

Bearer JWT via OAuth 2.0 client_credentials (scope `Integrationscope`).

## Steps

1. **Look up the business** — `GET /api/v2/kyb/check-cr/{commercialNumber}` returns owner information, branch data, authorized signatories, and real-time Zakat/Tax status. (`v1` returns basic CR details.)
2. **Verify the individual** — `GET /api/v1/kyb/absher-otp/{commercialNumber}/{nationalId}` initiates an Absher OTP proving the person is authorized for that CR. Use `GET /api/v1/kyb/absher-otp/{nationalId}` for individual-only verification.
3. **Confirm delegated authority** — `GET /api/v1/kyb/delegacy/{nationalId}/{delegatedReferenceId}` verifies a power-of-attorney so a delegate can sign on the principal's behalf.
4. **(Optional) National address** — `GET /api/v1/kyb/spl/national-address/{crNumber}` retrieves the Saudi Post national address for the business.

## Rules

- Only proceed to a signing invitation once CR + signatory authority are confirmed.
- These endpoints call live government sources; expect latency and handle `{ errorCode, message }` errors and `404` (not found).
