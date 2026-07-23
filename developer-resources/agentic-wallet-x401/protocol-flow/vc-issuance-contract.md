# VC issuance contract: MBI `POST /v1/vc/pay/apply` (two-phase)

Issuance is a **single, two-phase endpoint** on the MBI RS; behaviour branches on the presence of the `X-PAYMENT` header. The holder **signs the VC payload up front** (folding the legacy construct-sign/submit-sign into one call), so MBI can prove the request comes from the holder both before and after payment.

## Request body (both phases)

Mirrors the canonical VC payload:

```json
{
  "data": "[{\"templateId\":\"<fixed>\",\"metadata\":{ …attribute values… }}]",  // canonical JSON string, holder-signed
  "signData": "3044...hex...",   // holder Ed25519 signature over `data`
  "publicKey": "b0014...hex...", // holder public key → derives the subject address
  "expirationDate": "2027-01-01" // optional
}
```

* **Phase 1** (no `X-PAYMENT`) → MBI verifies the holder signature, then returns 402 with `accepts[]` (and `extra.paymentId`). A request not signed by a real holder is rejected before any 402.
* **Phase 2** (with `X-PAYMENT`, `paymentId` echoed in the body) → MBI re-verifies the holder signature, **asserts the VC-payload signer == the x402 payer** (one account both pays and owns the VC), verifies the amount exactly, then verify + settle via the Facilitator, and **issues the VC on settlement success**.

**Success (200 OK, wrapped):** `{ vcId, paymentId, txHash, verifiableCredential }`.

## Key invariants

* **Subject = payer.** The VC subject DID is derived from `publicKey`; MBI asserts it equals the payment signer. There is **no `holderDid` field**.
* **Settlement-bound, idempotent issuance.** MBI issues once per settled payment (keyed by `payment_id` / `tx_hash`); a repeat call returns the already-issued VC without re-charging. `GET /v1/vc/pay/status/{paymentId}` recovers the outcome if the phase-2 response is lost.
* **Canonical-bytes caveat (G3).** `data`'s key order (`templateId` then `metadata`) and compact JSON must be **byte-identical** to MBI's `constructSignData`, or holder-signature verification fails. The holder signs the same bytes over Wallet BE's `/sign-blob`.
* **Free mode (MVP bypass, config-only).** Setting `mbi.x402.payment-required = false` skips both phases, MBI verifies the holder signature and issues directly (no 402, no `X-PAYMENT`). This is how a **no-payment** issuance is realised on the MBI path; it is a config toggle and no environment currently enables it (the MVP fee-free grace period ended 2026-07-14). *(The requirement designates **ZCert** as the MVP issuer; MBI's free mode is the equivalent mechanism on the paid-issuer path.)*
