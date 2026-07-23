# Key terms

**VC (Verifiable Credential)** — a tamper-proof, issuer-signed credential (W3C data model). In Zetrix it is dual-signed by the issuer with Ed25519 and BBS+ keys, which enables selective disclosure later.

**VP (Verifiable Presentation)** — the object the holder presents to a verifier, wrapping one or more VCs plus proofs bound to the verifier's challenge.

**DID / ZID** — a Decentralised Identifier; the Zetrix variant (`did:zid:…`) resolves to a DID document on-chain that carries the subject's public keys.

**DCQL (Digital Credentials Query Language)** — the JSON query a verifier uses to state which credential and which claims/constraints it needs. This is where a proof's required conditions are defined.

**BBS+ selective disclosure** — reveals only chosen claims from a VC while keeping the issuer signature verifiable. Producing it is a public-key re-randomisation of the issuer's signature — it needs no holder secret.

**Bulletproof range proof (BulletproofRangeProof2021)** — proves a numeric claim lies in a range (e.g. age ≥ 18) without revealing the value.

**Holder binding** — an Ed25519Signature2020 over the verifier's nonce, signed inside the HSM, proving the presenter controls the holder key. This is the only private-key operation the wallet performs during a proof.

**HSM (softHSM for the POC)** — PKCS#11 security module holding the holder key; interface-compatible with the future ms-zetrix hardware HSM (cutover is config-only).
