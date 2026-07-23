# 4.1 Roles

| Actor | Role |
| --- | --- |
| AI Agent (Avatar) | The autonomous agent and the app that fronts it. Makes requests, receives 401/402 challenges, drives the wallet, relays `PROOF-RESPONSE` / `X-PAYMENT`. Holds no keys. |
| Agentic Zetrix Wallet (MCP) | The orchestrator. Holds/looks up the agent's credentials, matches DCQL, derives proofs, and coordinates signing, issuance, proof, and payment. Delivered as an MCP server. |
| Wallet BE + softHSM | Custodial signing boundary. Holds the agent's (holder) key in a hardware-grade security module and signs on request via an open, password-gated API. The MCP never touches the key. |
| ZCert | The credential issuer for the MVP (Zetrix certificate / issuer-management service). Verifies the credential application and issues the Agent Credential — no payment. |
| MBI Resource Server (MYID issuer) | The credential issuer for the POC paid path. Both the x402 resource server and the issuer: serves `POST /v1/vc/pay/apply` (returns the 402, verifies/settles the payment via the Facilitator, then issues the VC directly — it holds the issuer key). Also serves the VP-derivation endpoints `POST /v1/vp/ext/create` + `/submit` used in the x401 proof leg. |
| API Resource Server (RS) | The protected Zetrix BaaS resource. Issues the 401 Proof-Required (x401) and 402 Payment Required (x402, usage) challenges and verifies them; it never issues credentials. |
| OID4VP Verifier | The Zetrix verification engine (`openid4vp-verifier-be`). Builds presentation requests (DCQL + nonce), verifies VPs in-process, and resolves identities from the on-chain ZID registry. |
| Facilitator | The x402 settlement handler (`ms-zetrix`: `POST /ztx/facilitator/verify`, `/settle`). Submits the payment transaction to chain and returns the `txHash`. Self-pay is synchronous — no polling. |
| ZID Resolver | Resolves an issuer's DID document to its BBS+/Ed25519 verification keys (`zid-resolver[-sandbox].zetrix.com`), used to build the OID4VP submit body. |
| Zetrix chain | The ledger. Anchors decentralised identities (ZID) and settles payments in JMYR (ZTP20) or native ZETRIX. |
