---
description: >-
  A protocol paper for credential-gated agentic access on Zetrix:
  proof-of-authority (x401) with composable payment (x402).
---

# Zetrix Agentic Wallet — x401 & x402

Autonomous agents now transact on behalf of humans and organizations. Two questions must be answerable at the HTTP boundary, per request, without a human in the loop: **can you pay?** (x402) and **who authorized this, and are they allowed?** (x401). This paper specifies how the **Zetrix Agentic Wallet** implements x401 as a native capability, reusing the same wallet service that already speaks x402.

## How this documentation is organized

The paper is split into standalone topics so each section can be read, linked, and reviewed on its own:

* **Protocol**, [Motivation](motivation/README.md), [Background](background.md), and an [x401 vs. x402](x401-vs-x402-at-a-glance.md) comparison.
* **Architecture**, [roles](architecture/roles.md), [key terms](architecture/key-terms.md), [component design](architecture/component-design.md), and the [crypto split](architecture/crypto-architecture-split.md).
* **Flows**, the [protocol flow](protocol-flow/README.md) (messages, endpoints, POC payment) and the [message flows](message-flows/README.md) (issuance, paid access, zero-knowledge proof).
* **Delivery**, the [implementation approach](implementation-approach/README.md), [security considerations](security-considerations.md), and [open items](open-items-and-decisions.md).
* **Reference**, the [appendix](appendix/README.md): endpoints, glossary, and source material.
