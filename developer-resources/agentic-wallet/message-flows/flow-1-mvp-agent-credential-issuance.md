# Flow 1: MVP agent credential issuance (no payment)

**Goal.** Bootstrap the agent's identity: set up the wallet and obtain a free **Agent Credential** from **ZCert**. Issuance is gated by an x401 exchange (the agent proves control of its wallet/DID); **no x402 payment** is involved in the MVP.

## Steps

* **Wallet setup.** The agent provisions an **Agentic Zetrix Wallet**: Wallet BE creates the holder keypair inside the HSM, and the wallet registers the holder **ZID** on-chain (DID document carrying the Ed25519 and BBS+ public keys).
* **Apply via x401.** The wallet applies to **ZCert** for the Agent Credential. ZCert issues an x401 Proof-Required challenge to confirm the applicant controls the wallet/DID; the wallet answers it (holder-binding signed in the HSM) over the OID4VP verifier. **No x402 payment**, MVP is free.
* **Verify & issue.** ZCert verifies the proof and **issues** the Agent Credential, dual-signing it with the issuer's Ed25519 and BBS+ keys.
* **Store.** The wallet returns the credential to the agent; it is retained for reuse (client/wallet-held).
