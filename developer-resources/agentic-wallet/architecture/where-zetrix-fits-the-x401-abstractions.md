# Where Zetrix fits the x401 abstractions

| x401 abstraction | Zetrix realization |
| --- | --- |
| Issuer identity | **BID-method DID** on Zetrix; issuer public keys resolvable from the DID document |
| Credential | **ZID Verifiable Credential** (national ID, business registration, accreditation, membership) |
| Credential format | `sd-jwt` (selective disclosure) or `jwt_vc_json`; `mso_mdoc` for mDL interop |
| Revocation / status | On-chain status-list contract; verifiers read status offline via cached snapshots |
| Trust anchoring | On-chain issuer registry contract; deterministic, tamper-evident, no central CA |
| Agent binding | Agent key registered as a scoped, revocable delegate under the Principal's DID |
