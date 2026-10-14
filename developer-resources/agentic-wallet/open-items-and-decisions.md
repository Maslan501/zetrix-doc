# Open items & decisions

* **Verifier-nonce binding.** The holder-binding proof must bind the verifier's nonce/challenge; today the VP is bound to a server-random nonce. This is a real backend change (`VpCommonService.createVp` / `setBbsBlsProof`), not a config toggle.
* **Sync result path.** x401 needs the verdict inline; confirm the RS reads the submit response (not a separate async callback).
* **Price source.** Subscription/usage amounts stated in the 402 challenges vs. fixed config.
* **Subscription lifetime.** Permanent, time-boxed, or renewable?
* **Finality vs latency.** Confirm the acceptable settlement finality depth before issuance.
* **VP format scope.** POC targets `ldp_vp` / `jwt_vp`; SD-JWT / mdoc are not yet verifiable, either scope them out or fund the crypto.
* **Asset choice — decided.** JMYR (ZTP20) is the POC asset; native ZETRIX is supported but disabled in config (re-enabling is config-only).
* **Session tokens (OAuth 2.0 token-exchange).** The overview and component design reference a reusable-session Bearer token issued after a successful proof, but no endpoint, request/response format or token lifetime is defined in Messages & endpoints or the Endpoint reference. Either specify these (and add them to both references) or remove the references.
* **Auto-disclosure policy.** Programmatic DCQL matching for low-sensitivity claims vs. human approval for KYC-grade disclosure.
