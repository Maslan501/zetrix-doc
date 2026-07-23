# Implementation approach

How the wallet is actually built: a thin orchestrator over one HSM-held key, the implemented MCP components, the signing model, the canonicalisation caveat, asset/settlement choices, and the MVP → POC → Future staging.

## Wallet build model: thin wallet

The Agentic Zetrix Wallet is a **DCQL matcher + HTTP orchestrator** over one HSM-held Zetrix key. It delegates VP/ZKP derivation to the MBI RS (`/v1/vp/ext/create` → HSM-sign the returned blob → `/vp/ext/submit`, `includeVp: true`) rather than embedding native BBS+/Bulletproof libraries, and it uses `x401-zetrix-client` / `x402-zetrix-client` for the protocol legs. A **fat wallet** (local proof derivation) is possible but only worthwhile for offline/detached operation.

**Custody of credentials, client-held, no wallet vault.** The current design does **not** persist VCs in the MCP. `subscribe_and_issue` returns the VC to the calling agent, which holds it and passes it back per call (`vc` on `prove_identity`). This removes a server-side secret store; the only secret the wallet protects is the one HSM-held Zetrix key.

### Build vs reuse

| Piece | Status |
| --- | --- |
| OID4VP request builder, DCQL, VP verification, ZID resolution | Reuse (verifier engine) |
| BBS+ selective disclosure + Bulletproof range proof derivation | Reuse (MBI `/v1/vp/ext/create`) |
| Custodial HSM signing (holder key) via Wallet BE | Build / adapt |
| 401 Proof-Required challenge + resource-server SDK | New |
| x402 payment (blob build + self-pay sign + Facilitator settle) | Reuse (`x402-zetrix-client`) + MBI RS |
| Issuer-key resolution for the OID4VP submit body | Build (ZID resolver client) |
| Verifier-nonce binding into the holder proof | New backend change (see [Open items & decisions](../open-items-and-decisions.md)) |

## Implemented components (agentic-wallet-mcp)

| Layer | Module | Responsibility |
| --- | --- | --- |
| Tools | `wallet_status`, `prove_identity`, `pay_and_fetch`, `subscribe_and_issue`, `create_holder_account` | The five MCP tools the agent calls. |
| Orchestrators | `subscribe.ts`, `prove.ts`, `pay.ts`, `onboard.ts` | Compose the flows (issue / prove / pay-per-use / onboarding). |
| Clients | `MbiClient` (`/v1/vc/pay/*`, `/v1/vp/ext/*`), `MbiVpAdapter` (x401 `VcProofProvider`), `WalletBeClient`, `ZidResolverClient`, `resolveIssuerProofKeys` | HTTP adapters + the VP-derivation bridge. |
| Libraries | `x401-zetrix-client` (OID4VP proof), `x402-zetrix-client` (payment / `PaymentEngine.pay` with an injected Wallet-BE signer) | Protocol engines. |

**Wallet BE signing surface (holder custody):** `POST /wallet/hsm/sign-blob { blob(hex), address, password }` (x402 payment + holder binding), `POST /wallet/hsm/sign-message` (UTF-8, for `/vp/ext/*` auth), `POST /account/create` (onboarding). All HTTP 200 with an `errorCode` envelope; the holder Ed25519 key never leaves the HSM.

## Signing model

All signing (holder-binding + x402 payment) goes **Wallet BE → softHSM** over an **open, password-gated** API (`{blob, address, password}`), no session token/JWT. The interface is kept identical to the future **ms-zetrix hardware HSM** so the upgrade is config-only.

## Canonicalisation caveat

VP and VC-payload JSON must **preserve key order** (no JCS). JCS reordering shifts BBS+ message indices and breaks proof verification; likewise, the issuance `data` must be byte-identical to MBI's `constructSignData` (G3). Use insertion-ordered maps end-to-end.

## Asset & settlement

Payments settle via the Facilitator in **JMYR (ZTP20)** or **native ZETRIX** (tech-team choice for the POC; currently JMYR only). For the POC, all payments route to the **VC issuer** account.

## Staging summary

* **MVP**, wallet setup + free Agent Credential from ZCert (x401 only).
* **POC**, paid API Subscription Credential + usage (x401 + x402, both settling to the VC issuer).
* **Future**, combined 401 + 402 on a single protected/paid request.
