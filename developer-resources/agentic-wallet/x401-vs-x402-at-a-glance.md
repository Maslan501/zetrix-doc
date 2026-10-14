# x401 vs. x402 at a glance

| Dimension | x402 | x401 |
| --- | --- | --- |
| HTTP status | 402 Payment Required | 401 Unauthorized |
| Question asked | *Can you pay?* | *Who authorized this, and are they allowed?* |
| Server → agent | `402 Payment Required` + `accepts[]` (x402 wire body) | `401 Proof-Required` + OID4VP request (DCQL + nonce) |
| Agent → server | `X-PAYMENT` header on the retry (response: `X-PAYMENT-RESPONSE`) | VP submit (`POST /v1/presentation/submit`) → `signed_result` (+ `X-Callback-Signature` / `X-Callback-Timestamp`) |
| Proof object | Signed payment authorization / on-chain transfer | Verifiable Presentation (VP) of one or more VCs |
| Verified against | Facilitator (`/verify`, `/settle`) | Issuer public keys (deterministic, offline) |
| Zetrix primitive | JMYR (ZTP20) transfer — POC asset; native ZETRIX supported but disabled in config — Facilitator settlement | ZID VC + BID DID + on-chain issuer/revocation registry |
| Reusable session | Payment channel / receipt | OAuth 2.0 token-exchange → short-lived Bearer token *(planned; endpoint, format and token lifetime not yet specified — see [Open items & decisions](open-items-and-decisions.md))* |

The two are **orthogonal and composable**. A high-value action typically requires *both*: authority (x401) then payment (x402).
