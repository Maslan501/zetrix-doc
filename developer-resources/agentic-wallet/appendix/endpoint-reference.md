# Endpoint reference

| Endpoint | Purpose |
| --- | --- |
| `POST /v1/verification/request` | RS/issuer creates an x401 verification request (Bearer key) |
| `GET /v1/presentation/{id}` | Wallet fetches DCQL + nonce + `response_uri` |
| `POST /v1/presentation/submit` | Wallet submits the VP; verifier verifies in-process |
| `POST /v1/presentation/abort` | Wallet ends a session it cannot complete |
| `POST /v1/vc/pay/apply` | MBI — x402 VC issuance (two-phase: 402 → verify+settle+issue) |
| `POST /v1/vc/pay/quote` | MBI — price + canonical `signPayload` to sign (optional pre-flight) |
| `GET /v1/vc/pay/status/{paymentId}` | MBI — idempotent issuance-outcome recovery |
| `POST /v1/vp/ext/create` · `POST /v1/vp/ext/submit` | MBI — derive the VP (BBS+ SD + range proof) for the x401 leg |
| `POST /ztx/facilitator/verify` · `/settle` | Facilitator — x402 payment verification + on-chain settlement (self-pay, sync) |
| `POST /wallet/hsm/sign-blob` · `/sign-message` · `/account/create` | Wallet BE — holder custody: sign + onboarding |
