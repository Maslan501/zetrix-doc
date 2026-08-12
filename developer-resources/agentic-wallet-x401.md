---
description: >-
  A protocol paper for credential-gated agentic access on Zetrix:
  proof-of-authority (x401) with composable payment (x402).
hidden: true
---

# Agentic Wallet

Autonomous agents now transact on behalf of humans and organizations. Two questions must be answerable at the HTTP boundary, per request, without a human in the loop: **can you pay?** (x402) and **who authorized this, and are they allowed?** (x401). This paper specifies how the **Zetrix Agentic Wallet** implements x401 as a native capability, reusing the same wallet service that already speaks x402.

## How this documentation is organized

The paper is split into standalone topics so each section can be read, linked, and reviewed on its own:

* **Protocol**, [Motivation](agentic-wallet/motivation/), [Background](agentic-wallet/background.md), and an [x401 vs. x402](agentic-wallet/x401-vs-x402-at-a-glance.md) comparison.
* **Architecture**, [roles](agentic-wallet/architecture/roles.md), [key terms](agentic-wallet/architecture/key-terms.md), [component design](agentic-wallet/architecture/component-design.md), and the [crypto split](agentic-wallet/architecture/crypto-architecture-split.md).
* **Flows**, the [protocol flow](agentic-wallet/protocol-flow/) (messages, endpoints, POC payment) and the [message flows](agentic-wallet/message-flows/) (issuance, paid access, zero-knowledge proof).
* **Delivery**, the [implementation approach](agentic-wallet/implementation-approach.md), [security considerations](agentic-wallet/security-considerations.md), and [open items](agentic-wallet/open-items-and-decisions.md).
* **Reference**, the [appendix](agentic-wallet/appendix/): endpoints, glossary, and source material.
