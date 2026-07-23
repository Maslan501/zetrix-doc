# 7.1 Wallet build model — thin wallet

The Agentic Zetrix Wallet is a **DCQL matcher + HTTP orchestrator** over one HSM-held Zetrix key. It delegates VP/ZKP derivation to the MBI RS (`/v1/vp/ext/create` → HSM-sign the returned blob → `/vp/ext/submit`, `includeVp: true`) rather than embedding native BBS+/Bulletproof libraries, and it uses `x401-zetrix-client` / `x402-zetrix-client` for the protocol legs. A **fat wallet** (local proof derivation) is possible but only worthwhile for offline/detached operation.

**Custody of credentials — client-held, no wallet vault.** The current design does **not** persist VCs in the MCP. `subscribe_and_issue` returns the VC to the calling agent, which holds it and passes it back per call (`vc` on `prove_identity`). This removes a server-side secret store; the only secret the wallet protects is the one HSM-held Zetrix key.

## Build vs reuse

| Piece | Status |
| --- | --- |
| OID4VP request builder, DCQL, VP verification, ZID resolution | Reuse (verifier engine) |
| BBS+ selective disclosure + Bulletproof range proof derivation | Reuse (MBI `/v1/vp/ext/create`) |
| Custodial HSM signing (holder key) via Wallet BE | Build / adapt |
| 401 Proof-Required challenge + resource-server SDK | New |
| x402 payment (blob build + self-pay sign + Facilitator settle) | Reuse (`x402-zetrix-client`) + MBI RS |
| Issuer-key resolution for the OID4VP submit body | Build (ZID resolver client) |
| Verifier-nonce binding into the holder proof | New backend change (see [§9](../open-items-and-decisions.md)) |
