---
description: A high-performance scaling layer that extends Zetrix with lower fees and higher throughput.
---

# Zetrix Layer 2 (Z2)

## Zetrix Layer 2 (Z2)

Zetrix Layer 2 (Z2) is a scaling layer built on top of the Zetrix Layer 1 blockchain. It processes transactions off the main chain and settles the results back to Layer 1, giving applications higher throughput and lower fees while inheriting the security of the Zetrix mainnet.

Z2 is designed for use cases that need fast, low-cost transactions at scale — such as high-volume payments, consumer dApps, and enterprise workloads — without compromising the trust guarantees of the underlying Layer 1.

### Why a Layer 2

As on-chain activity grows, executing every transaction directly on Layer 1 becomes slower and more expensive. A Layer 2 addresses this by:

* **Scaling throughput** — transactions are executed on Z2 and batched, greatly increasing capacity beyond what Layer 1 handles alone.
* **Reducing cost** — batching and off-chain execution lower the gas cost per transaction for end users.
* **Preserving security** — final state is anchored back to Zetrix Layer 1, so Z2 inherits the settlement guarantees of the mainnet.
* **Keeping compatibility** — developers can build on Z2 using familiar Zetrix tooling and smart contract patterns.

### Who should use it

Z2 is for developers and enterprises building applications that require:

* High transaction volume with predictable, low fees
* Fast confirmation for interactive or consumer-facing experiences
* Settlement assurance backed by the Zetrix Layer 1 network

### How it fits with Layer 1

Layer 1 remains the source of truth and final settlement layer. Z2 handles execution at scale and periodically commits its state to Layer 1. Assets and data can move between the two layers, letting you choose the layer that best fits each part of your application.

### Get started

The detailed documentation covers the Z2 architecture, network endpoints, bridging, and integration steps.

<a href="https://docs.zetrix.com/zetrix-l2-documentation" class="button primary">Open Zetrix Layer 2 (Z2) documentation</a>
