# 5.2 Messages & endpoints

## (1) The 401 Proof-Required challenge

Returned inline by the RS/issuer:

```http
HTTP/1.1 401 Proof-Required

{
  "verification_data": { ... },          // reference to the OID4VP request
  "credential_requirements": { ... },    // human/agent-readable summary
  "request_id": "pres_abc123"            // correlates the session
}
```

## (2) Fetch the presentation definition

`GET /v1/presentation/{presentationId}`:

```json
{
  "object": {
    "presentationId": "pres_abc123",
    "credentialQuery": { "credentials": [ /* DCQL — see §6, Flow 3 */ ] },
    "nonce": "n-0S6_WzA2Mj",
    "responseUri": "https://verifier/v1/presentation/submit",
    "abortUri": "https://verifier/api/v1/presentation/abort",
    "responseMode": "direct_post",
    "state": "eyJhbGciOiJSU..."
  },
  "success": true
}
```

## (3) Submit the VP

`POST /v1/presentation/submit`:

```json
{
  "vp_token": { /* the VP: disclosed claims + BBS+ proof + range proof + holder binding */ },
  "presentation_submission": { "id": "...", "definition_id": "pres_abc123", "descriptor_map": [ ... ] },
  "presentation_id": "pres_abc123",
  "ed25519_public_key": "…",
  "bbs_public_key": "…"
}
```

## (4) The PROOF-RESPONSE

The signed verdict the caller verifies. In sync-HMAC mode (the agentic mode, when the request was created with `callbackUrl` omitted) the verifier returns the signed result plus HMAC headers, which the wallet relays verbatim:

* **Body** — `signed_result` — `{ presentationId, verified, status, verifiedClaims }`
* **Headers** — `X-Callback-Signature = HMAC-SHA256(timestamp + "." + payload)` and `X-Callback-Timestamp`, computed with the RS client's shared `callbackSecret`.

## Endpoint summary

| Endpoint | Caller | Auth |
| --- | --- | --- |
| `POST /v1/verification/request` | RS / issuer (x401 SDK) | Bearer API key |
| `GET /v1/presentation/{id}` | Wallet | none (public) |
| `POST /v1/presentation/submit` | Wallet | wallet signature headers |
| `POST /v1/presentation/abort` | Wallet | state binding |
