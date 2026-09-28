# TradeTrust API

## TradeTrust API overview

The TradeTrust API lets applications create and manage TradeTrust document workflows.

It is part of the `myeg-z2-service` platform. The API follows OpenAPI `3.1.0`.

Use it to integrate document issuance, verification, and related TradeTrust operations.

### Get started

1. Obtain a JWT from the shared BaaS authentication service.
2. Select the server that matches your environment.
3. Send the bearer token with every API request.
4. Include an API key for sandbox and v2 gateway endpoints.

### Request authentication

All TradeTrust endpoints require a bearer token. Gateway-hosted environments may also require an API key.

#### `bearerAuth`

* **Type:** HTTP `bearer` (JWT)

Send the OAuth2 JWT in the `Authorization` header:

```http
Authorization: Bearer <access-token>
```

Authentication runs before request routing. Unauthenticated requests return `401`, including unknown paths.

#### `apiKeyAuth`

* **Type:** API key in header `X-API-Key`

The AWS API Gateway key is required on `api-sandbox` and `api-v2` servers.

```http
X-API-Key: <api-key>
```

This key does not replace the bearer token. The gateway validates it before requests reach this service. Omit it when port-forwarding to a pod.

### API reference

Use the endpoint reference for operation paths, request schemas, and response details.

***
