# POC settlement routing

**POC decision.** For the POC, **all** x402 payments — subscription and usage — are routed to the **Verifiable Credential issuer** (`payTo` = VC issuer account). Payments may use **JMYR (ZTP20)** or the **native Zetrix token**; the technical team selects the asset for the POC. **Currently only a single JMYR (ZTP20) `accepts[]` entry is returned** — native ZTX is temporarily disabled in config and re-enabling it is config-only. In a later stage, usage payments may route to the individual resource server instead.
