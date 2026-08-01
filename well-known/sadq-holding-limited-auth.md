# Auth.md — Sadq Integration API

> Agent registration and authentication instructions for the Sadq e-signature platform.

## Overview

The Sadq Integration API uses OAuth 2.0 Bearer tokens. AI agents and services
authenticate using the **client_credentials** grant with a Client ID and Client Secret.

- **API documentation**: https://docs.sadq.sa/docs
- **OpenAPI schema**: https://docs.sadq.sa/api/schema
- **Authorization server**: https://identity.sadq.sa

---

## Supported Identity Types

| Type | Description |
|------|-------------|
| `service` | Server-to-server integration using client credentials |
| `agent` | Autonomous AI agent acting on behalf of an account |

---

## Credential Types

| Type | Description |
|------|-------------|
| `client_credentials` | OAuth 2.0 Client ID + Client Secret (recommended for agents) |

---

## Registration

Register for an integration account to obtain a Client ID and Client Secret:

**Register**: https://sadq.sa/register

Once registered you will receive:
- `client_id` — your integration client identifier
- `client_secret` — your integration client secret

---

## Obtaining a Token

```http
POST https://identity.sadq.sa/connect/token
Content-Type: application/x-www-form-urlencoded

grant_type=client_credentials
&client_id={YOUR_CLIENT_ID}
&client_secret={YOUR_CLIENT_SECRET}
&scope=Integrationscope
```

**Response:**
```json
{
  "access_token": "eyJhbGci...",
  "token_type": "Bearer",
  "expires_in": 99200,
  "scope": "Integrationscope"
}
```

---

## Using the Token

Include the token in the `Authorization` header on every API request:

```http
GET https://api.sadq.sa/api/v1/...
Authorization: Bearer {access_token}
Content-Type: application/json
```

---

## Token Revocation

Revoke a token when your agent session ends:

```http
POST https://identity.sadq.sa/connect/revocation
Content-Type: application/x-www-form-urlencoded

token={access_token}
&client_id={YOUR_CLIENT_ID}
&client_secret={YOUR_CLIENT_SECRET}
```

---

## Scopes

| Scope | Description |
|-------|-------------|
| `Integrationscope` | Full access to the Sadq Integration API |

---

## Discovery Endpoints

| Endpoint | Description |
|----------|-------------|
| `https://docs.sadq.sa/.well-known/oauth-authorization-server` | OAuth 2.0 AS metadata (RFC 8414) |
| `https://docs.sadq.sa/.well-known/openid-configuration` | OIDC discovery (OIDC 1.0) |
| `https://docs.sadq.sa/.well-known/oauth-protected-resource` | Protected resource metadata (RFC 9728) |
| `https://docs.sadq.sa/.well-known/api-catalog` | API catalog (RFC 9727) |

---

## Support

- **Documentation**: https://docs.sadq.sa/docs
- **Contact**: support@sadq.sa
- **Website**: https://sadq.sa
