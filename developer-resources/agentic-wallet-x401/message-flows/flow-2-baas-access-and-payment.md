# Flow 2 — Zetrix BaaS access and payment (x401 + x402)

**Goal.** The agent accesses a protected, paid Zetrix BaaS resource. If it lacks the required **API Subscription Credential**, it buys one via x402, then proves it via x401 and is granted access.

## Steps (mapped to the requirement)

* The agent requests a protected Zetrix BaaS resource.
* The RS triggers **x401**, asking for the required credential/proof (401 Proof-Required).
* The wallet checks whether it already holds the **API Subscription Credential**. If yes → skip to (g).
* If not, the wallet applies to the **MBI RS** (`POST /v1/vc/pay/apply`, phase 1) with the holder-signed VC payload → 402 + `paymentId`.
* **x402** pays for the subscription (self-pay transfer built and signed via Wallet BE/HSM); the wallet retries apply (phase 2) with `X-PAYMENT` + the echoed `paymentId`.
* MBI verifies the holder signature and payment, settles via the Facilitator, and **issues the API Subscription Credential** as a VC (settlement-bound, idempotent).
* The agent retries the BaaS request and presents the credential through **x401** (VP derived via MBI `/vp/ext/*`, submitted to the OID4VP verifier).
* The RS verifies the credential (via the relayed signed result) before granting access.

**Future access to protected & paid resources.** A resource may require **both**: **x401** (proof of the required credential/authorisation) **and** **x402** (payment for the API/resource usage) on the same retry — the identity proof and the usage payment travel together. Per the POC decision, both subscription and usage payments settle to the VC issuer.
