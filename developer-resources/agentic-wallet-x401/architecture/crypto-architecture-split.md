# 4.3 Crypto architecture split

Standard HSMs cannot derive the zero-knowledge / selective-disclosure proofs (BBS+, Bulletproof range-proofs) that privacy-preserving VCs require. Responsibilities therefore split:

| Operation | Where it runs |
| --- | --- |
| Selective-disclosure proof derivation (BBS+, range-proofs) from VCs | **Software**, inside the Agentic Wallet MCP |
| Holder-binding signature (Ed25519 over the request nonce) | **SoftHSM** holder key (PKCS#11), via the Wallet BE — password-gated, no session tokens |
| x402 payment blob signing (blob unsigned until HSM signing) | **SoftHSM** holder key, via the Wallet BE |
| Issuer signature (VC issuance) | **MBI / MYID issuer** — Ed25519 + BBS+ |
| In-process VP verification (signature, BBS+, range-proof, DCQL) | **OID4VP Verifier** |
| Issuer public-key resolution | **On-chain ZID Registry** (direct read) |

Private keys never leave the SoftHSM / Wallet BE boundary; the MCP requests signatures, it does not hold signing keys. The x402 payment blob is assembled unsigned and only signed at the HSM step.
