# 7.2 Implemented components (agentic-wallet-mcp)

| Layer | Module | Responsibility |
| --- | --- | --- |
| Tools | `wallet_status`, `prove_identity`, `pay_and_fetch`, `subscribe_and_issue`, `create_holder_account` | The five MCP tools the agent calls. |
| Orchestrators | `subscribe.ts`, `prove.ts`, `pay.ts`, `onboard.ts` | Compose the flows (issue / prove / pay-per-use / onboarding). |
| Clients | `MbiClient` (`/v1/vc/pay/*`, `/v1/vp/ext/*`), `MbiVpAdapter` (x401 `VcProofProvider`), `WalletBeClient`, `ZidResolverClient`, `resolveIssuerProofKeys` | HTTP adapters + the VP-derivation bridge. |
| Libraries | `x401-zetrix-client` (OID4VP proof), `x402-zetrix-client` (payment / `PaymentEngine.pay` with an injected Wallet-BE signer) | Protocol engines. |

**Wallet BE signing surface (holder custody):** `POST /wallet/hsm/sign-blob { blob(hex), address, password }` (x402 payment + holder binding), `POST /wallet/hsm/sign-message` (UTF-8, for `/vp/ext/*` auth), `POST /account/create` (onboarding). All HTTP 200 with an `errorCode` envelope; the holder Ed25519 key never leaves the HSM.
