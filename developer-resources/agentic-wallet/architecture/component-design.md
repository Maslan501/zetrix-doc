# Component design

The implementation splits into two new components to build plus existing Zetrix wallet infrastructure.

<figure><img src="../../../.gitbook/assets/Agentix Architecture-2026-07-23-062805.png" alt=""><figcaption></figcaption></figure>

## To build

* **Agentic Wallet MCP (Wallet / ZID)**, the credential manager per the x401 spec. Holds credentials, receives OpenID4VP requests, selects matching VCs, **derives selective-disclosure proofs in software** (BBS+ / range-proofs), orchestrates holder-binding signatures via the Wallet BE, and returns the credential presentation result.
* **X401 SDK (Java / Node.js)**, the Resource Server–side library. Issues the 401 + challenge, validates returned proof artifacts on every request, runs the OAuth 2.0 token-exchange endpoint, and coordinates with the X402 SDK via a shared route registry ([Result delivery](../protocol-flow/result-delivery.md)).

## Agent Runtime (wallet side, new build)

* **Agentic Wallet MCP / Wallet BE**, credential manager; holds credentials, derives SD proofs in software (BBS+/range), and **signs x402 payment blobs** via the holder key.
* **SoftHSM (holder key, PKCS#11)**, software HSM holding the holder signing key, reached over PKCS#11. Calls are **password-gated (no session tokens)**; no direct MCP→SoftHSM path.
* **Vault**, secure storage for the agent's Verifiable Credentials.
* **Avatar**, the user-facing agentic wallet app.

## Resource Servers

* **MBI Resource Server (MYID issuer)**, issues the identity VC and, for paid issuance (P1, [POC payment architecture](../protocol-flow/poc-payment-architecture.md)), **settles the payment and issues the VC in one place** (holds the issuer key). Signs with Ed25519 + BBS+.
* **API Resource Server**, gates protected APIs; handles x401 + x402 **pay-per-use** (P2, [POC payment architecture](../protocol-flow/poc-payment-architecture.md)).

## Shared / existing infrastructure (reused)

* **OID4VP Server / Verifier**, decodes the JAR, resolves the `dcql_query`, and validates VPs in-process (BBS+, range-proof, DCQL). Service classes: `PresentationServiceImpl` (in-process handler, `POST /presentation/submit`), `VpCommonService` (`createVp`), `CallbackService` (result-signature generation).
* **Credential Engine**, Zplatform VC Engine; issues/stores VCs. **Explicitly excluded from the verify flow.**
* **DID Resolver**, Zplatform service; resolves DIDs (BID method) to issuer public keys.
* **X402 SDK (V2), Facilitator**, the payment leg; the Facilitator exposes `/supported`, `/verify`, `/settle`.
* **On-chain ZID Registry**, supplies issuer public keys directly, so no external credential-engine round-trip is needed at verify time.
