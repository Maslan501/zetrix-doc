# Result delivery

| Mode | Trigger | Who verifies the result |
| --- | --- | --- |
| Sync-HMAC (agentic) | Verification request created with no `callbackUrl` | The RS, after the wallet relays the signed result + HMAC |
| Async callback (legacy) | `callbackUrl` provided | The callback receiver |

The wallet never holds the `callbackSecret`; it only relays the signed artifact. Integrity rests entirely on the HMAC, and the RS must reject a missing/invalid signature or a stale timestamp.
