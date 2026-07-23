# x401 vs. x402 at a glance

| Dimension | x402 | x401 |
| --- | --- | --- |
| HTTP status | 402 Payment Required | 401 Unauthorized |
| Question asked | *Can you pay?* | *Who authorized this, and are they allowed?* |
| Server → agent | `PAYMENT-REQUIRED` | `PROOF-REQUEST` / `PROOF-REQUIRED` |
| Agent → server | `PAYMENT-SIGNATURE` | `PROOF-RESPONSE` / `PROOF-PRESENTATION` |
| Proof object | Signed payment authorization / on-chain transfer | Verifiable Presentation (VP) of one or more VCs |
| Verified against | Facilitator (`/verify`, `/settle`) | Issuer public keys (deterministic, offline) |
| Zetrix primitive | ZETRIX / JMYR (ZTP20) transfer, Facilitator settlement | ZID VC + BID DID + on-chain issuer/revocation registry |
| Reusable session | Payment channel / receipt | OAuth 2.0 token-exchange → short-lived Bearer token |

The two are **orthogonal and composable**. A high-value action typically requires *both*: authority (x401) then payment (x402).
