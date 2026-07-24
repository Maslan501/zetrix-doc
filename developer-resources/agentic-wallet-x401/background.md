# Background: the x401 protocol

x401 is an **open, issuer-neutral** protocol. Key characteristics drawn from the public specification and launch materials:

* **Challenge-response over HTTP**, deliberately mirroring x402's five-step shape.
* **Verifiable Credentials, not sessions or API keys.** Proofs are cryptographically signed assertions from an **Issuer**; verification is deterministic against the issuer's public keys, requiring **no shared secret and no live round-trip** to the issuer.
* **Humans authorize; agents inherit.** x401 gives agents **no independent legal identity**. An agent presents proof that a verified human (or org) authorized this scoped action, preserving the accountability chain that legal frameworks (e.g., ESIGN/UETA) require.
* **Built on open identity standards:** the credential requirement is an **OpenID4VP** request carrying a **DCQL** query. It accepts any OpenID4VP-expressible format: `jwt_vc_json`, `sd-jwt`, `mso_mdoc`, and others.
* **Privacy-preserving:** selective disclosure and zero-knowledge proofs allow answering "over 18?" or "accredited?" without revealing the underlying data.
* **Composable with x402:** identity and payment remain distinct flows; a server may require either, or both in sequence.

x401 was launched by Proof in June 2026 with technical contribution from Circle, OpenAI, Google, and Okta; the normative spec lives at x401.id. A separate community project, **claw401**, explores a wallet-authentication variant (Ed25519 canonical-JSON challenge/response with single-use TTL-bounded nonces, replay caching, scoped agent sessions) that informs our replay-resistance design in [Implementation approach](implementation-approach/).
