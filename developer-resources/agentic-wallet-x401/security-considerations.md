# Security considerations

* **Replay.** Bind the verifier nonce into the holder proof; enforce single-submission and short session TTLs. x402 uses a per-payment nonce to prevent double-spend.
* **Custody.** No user-held keys; one HSM-held holder key; issuer keys held only by issuers. Password-gated signing avoids long-lived tokens.
* **Data minimisation.** Selective disclosure + range proofs mean the verifier learns only that constraints are satisfied — not the underlying values.
* **On-chain trust anchor.** Verification keys and revocation status resolve on-chain, not from the untrusted agent path.
* **Auditability.** Every payment is a settled Zetrix transaction (`txHash`); every verification produces a signed, HMAC-authenticated result.
* **Fail-closed.** Missing/invalid signatures, expired sessions, or revoked credentials must deny access.
