# 7.3 Signing model

All signing (holder-binding + x402 payment) goes **Wallet BE → softHSM** over an **open, password-gated** API (`{blob, address, password}`) — no session token/JWT. The interface is kept identical to the future **ms-zetrix hardware HSM** so the upgrade is config-only.
