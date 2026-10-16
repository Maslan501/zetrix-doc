# Where Zetrix fits the x401 abstractions

| x401 abstraction | Zetrix realization |
| --- | --- |
| Issuer identity | Zetrix **`did:zid`** DID (a Blockchain-based Identifier, BID); issuer public keys resolvable from the DID document |
| Credential | **ZID Verifiable Credential** (national ID, business registration, accreditation, membership) |
| Credential format | **Verifiable today (POC):** `ldp_vp` / `jwt_vp`. **Planned (not yet verifiable):** `sd-jwt`, `jwt_vc_json`, `mso_mdoc` (mDL interop) — see [Open items & decisions](../open-items-and-decisions.md) |
| Revocation / status | On-chain revocation registry; revoking a VC submits a signed revocation blob to the blockchain, and status is checked against the blockchain revocation registry **at verification time** (no cached-snapshot staleness) |
| Trust anchoring | On-chain issuer registry contract; deterministic, tamper-evident, no central CA |
| Agent binding | Agent key registered as a scoped, revocable delegate under the Principal's DID |
