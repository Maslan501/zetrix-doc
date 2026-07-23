# Flow 3 — zero-knowledge proof flow

**Goal.** Let the agent prove it satisfies a credential/authorisation requirement **without revealing the full credential or unnecessary information** — e.g. prove "age ≥ 18" or "membership is active" while disclosing nothing else.

Two complementary zero-knowledge mechanisms are used, both derived from the issuer's existing signature and requiring **no holder secret** beyond the holder-binding:

* **BBS+ selective disclosure** — reveal only the specific claims requested; the issuer's signature stays verifiable over the disclosed subset.
* **Bulletproof range proof** (BulletproofRangeProof2021) — prove a numeric claim lies within a range **without disclosing the value**.

## How the required claims/conditions are defined

The verifier states its requirements in **DCQL**. Each credential query names a format, an optional credential type, and a list of claims with **constraints**:

```json
{
  "credentials": [
    {
      "id": "age_check",
      "format": "ldp_vc",
      "credentialType": "NationalIDCredential",
      "claims": [
        { "path": ["identityCardMalaysia", "age"], "constraints": { "minimum": 18 } }
      ]
    }
  ]
}
```

Supported constraints: `minimum`, `maximum` (numeric → drive range proofs), `enum`, `pattern` (strings), and `required`. A `minimum`/`maximum` constraint is the signal to prove a **range** rather than disclose the value.

## How the agent generates the proof from its credential

The current wallet is a **thin wallet**: it does not embed the BBS+/Bulletproof native libraries. It maps the request, delegates the zero-knowledge derivation to the MBI RS's `/vp/ext/create`, and performs the one private-key operation (holder binding) inside the HSM.

* **Match & map.** The wallet matches the DCQL against the held credential and maps it to a **reveal set** (`revealAttributes` — dotted paths to disclose) plus an optional **rangeProof** request for numeric constraints.
* **Selective disclosure (BBS+) — server-assisted.** `POST /v1/vp/ext/create {vc, revealAttributes, rangeProof?}` returns an unsigned VP blob. MBI derives the `BbsBlsSignatureProof2020` by **re-randomising the issuer's BBS+ signature over only the disclosed messages, using the issuer's public key — no BBS+ private key is involved**. Field order is preserved (no JCS canonicalisation), or the BBS+ message indices shift and verification fails.
* **Range proof (Bulletproof).** When a numeric constraint is present, a `BulletproofRangeProof2021` is included, proving the committed value satisfies the bound **without revealing it**:

```json
{
  "rangeProof": {
    "type": "BulletproofRangeProof2021",
    "range": [ { "attribute": "identityCardMalaysia.age", "min": 18 } ],
    "proofValue": "u…",
    "bits": 32,
    "domain": "age-range-proof",
    "commitments": ["uPkmj7WeaNx…"]
  }
}
```

* **Holder binding.** The wallet Ed25519-signs the returned blob **inside the HSM** (via Wallet BE) — the only private-key operation — then `POST /v1/vp/ext/submit {blobId, signedBlob, includeVp: true}` returns the finished VP. This binds the presentation to the holder key.
* **Issuer keys.** The wallet resolves the VC issuer's BBS+/Ed25519 verification keys via the **ZID resolver** and includes them in the OID4VP submit body (the verifier checks each VC's issuer-signed proof against these; its server-side DID-resolution fallback is not implemented).

**Fat-wallet alternative.** A wallet could instead embed the BBS+ + Bulletproof libraries and derive the proof locally over one HSM-held key. This is possible but only worthwhile for offline/detached operation; the POC uses the thin (server-assisted) model above.

## How the proof is submitted through x401

The assembled VP (disclosed claims + BBS+ proof + range proof + holder binding) is submitted via `POST /v1/presentation/submit` with the holder's `ed25519_public_key` and `bbs_public_key`, correlated by `presentation_id`. This is the same submit step as any x401 exchange.

## How the resource server verifies the proof

The OID4VP verifier verifies **in-process** (`PresentationServiceImpl.verifyVp` → `vpCommonService.verifyVpLite`):

* **Signatures** — BBS+ derived proof and Ed25519 holder binding, against keys **resolved from the on-chain ZID registry** (not trusted from the request).
* **Range proof** — the Bulletproof is checked against the stated bounds and domain.
* **DCQL constraints** — the disclosed claims satisfy the requested paths/constraints.
* **Nonce/replay** — the holder binding must match the session nonce; VP single-submission is enforced.

The verifier returns a **signed result** (`verified`, `status`, `verifiedClaims`); the RS accepts access on `verified = true`.

## How expiry, validity and revocation are checked

* **Expiry / validity** — the wallet screens `expirationDate` / `validFrom` before presenting; the verifier's `verifyVpLite` independently checks **expiration**. Separately, the **OID4VP session** carries a short TTL (`expiresAt`); a stale session is rejected (`VERIFICATION_REQUEST_EXPIRED`), bounding the replay window for sub-second agent round-trips.
* **Revocation** — `verifyVpLite` checks **revocation** as part of verification; credential status and issuer keys resolve against the on-chain ZID registry, so an issuer's revocation is reflected at verification time.
* **Fail-closed** — a missing/invalid signature, failed range proof, expired session, or revoked credential yields `verified = false`; the RS must deny access. The wallet may call the abort endpoint (`credential_expired`, `credential_not_found`, …) to end the session cleanly.
