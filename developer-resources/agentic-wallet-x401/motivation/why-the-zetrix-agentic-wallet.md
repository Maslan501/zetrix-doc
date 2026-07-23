# Why the Zetrix Agentic Wallet

Zetrix is uniquely positioned to host both protocols in one wallet:

* **It already issues verifiable identity.** Through **ZID**, Zetrix digitizes national IDs and business-registration credentials into W3C Verifiable Credentials, anchored by the **BID** DID method (registered in the W3C DID Specification Registry). This is exactly the issuer/credential infrastructure x401 assumes but does not itself provide.
* **It already moves value.** The same wallet that settles x402 payments (in native ZETRIX or JMYR, a ZTP20 token) can attach the identity proof x401 demands — one agent, one wallet, two challenge types.
* **It is chain-anchored and cross-border.** On-chain issuer registries and revocation lists give x401 a tamper-evident, globally reachable trust root without a central authority.

## Delivery staging

This paper describes three progressive stages so readers can distinguish what is required now from what comes later:

| Stage | Identity (x401) | Payment (x402) | Credential | Issuer |
| --- | --- | --- | --- | --- |
| MVP | Yes | None (free) | Agent Credential | ZCert |
| POC | Yes | Subscription + usage | API Subscription Credential | Zetrix BaaS VC issuer |
| Future | Yes | Per-access (proof + pay) | Any required credential | Per-resource |
