---
description: >-
  An EVM-equivalent optimistic rollup that scales Zetrix with higher throughput
  and lower fees while settling to Layer 1.
---

# Zetrix Layer 2 (Z2)

## Zetrix Layer 2 (Z2)

Zetrix Layer 2 (Z2) is an **optimistic rollup** built on top of the Zetrix Layer 1 (L1) blockchain. It executes transactions off-chain and periodically anchors compressed state back to L1, delivering orders-of-magnitude higher throughput and lower per-transaction cost while inheriting the security and final settlement guarantees of the Zetrix mainnet.

Z2 pairs the optimistic rollup model with an **AnyTrust-style Data Availability Committee (DAC)**: instead of posting all transaction data on-chain, a committee attests to batch data, with automatic fallback to L1 calldata if the consensus threshold is not met.

{% hint style="info" %}
Z2 is currently on **Testnet**. Mainnet is planned. The linked documentation is a living document and may change as the network evolves.
{% endhint %}

### Why a Layer 2

Executing every transaction directly on L1 runs into four core constraints. Z2 is designed to overcome them while preserving L1-enforced correctness:

* **Finite block space** — execution moves off L1, freeing mainnet capacity.
* **Rising gas costs** — batching and compression lower the cost per transaction.
* **Confirmation latency** — off-chain execution gives fast soft confirmation.
* **Unbounded state growth** — only compressed state commitments are anchored to L1.

### Key features

* **EVM-equivalent** — build with standard Ethereum tooling such as MetaMask, ethers.js, Hardhat, and Foundry.
* **QBFT consensus** — canonical block production via go-quorum QBFT nodes, with independent Besu re-executors for cross-client verification.
* **Off-chain data availability** — data is published to the DAC by default, with automatic L1 calldata fallback.
* **Fraud-proof security** — a Proposer posts state assertions; independent Watchers can dispute mismatches on L1 during the challenge window before final settlement.
* **Native asset bridging** — move ZETRIX and ZTP20 assets between L1 and L2.
* **Open participation** — validators and independent verifiers (Watchers) can take part in the network.

### Architecture at a glance

Z2 is organized into four planes:

1. **L2 Execution Plane** — go-quorum QBFT nodes, Sequencers, and independent Besu re-executors handle transaction ordering and block production.
2. **Data Availability Plane** — a Data Availability Committee (a 5-member committee requiring 3-of-5 consensus) attests to and verifies batch data.
3. **Settlement Layer (Zetrix L1)** — records batch commitments, holds state assertions, manages bridged assets, and resolves disputes.
4. **Services Plane** — Proposers, Watchers, Relayers, and operational tooling provide assertion proposals, verification, bridging, and observability.

**Transaction flow:** a Sequencer orders transactions into batches, posts a commitment to the L1 inbox, and distributes the data to the DAC. The Proposer then submits state assertions, which the committee verifies for soft confirmation before final settlement on L1. Watchers can dispute an invalid assertion during the challenge window.

### Who should use it

The detailed documentation targets node operators, validators, integrators, infrastructure engineers, and independent verifiers (Watchers), and covers architecture, deployment, operation, and security.

### Get started

The detailed documentation covers the Z2 architecture and trust model, network endpoints and deployment, wallet configuration, asset flow and tokenomics, reliability and security, and the API reference.

<a href="https://docs.zetrix.com/zetrix-l2-documentation" class="button primary">Open Zetrix Layer 2 (Z2) documentation</a>
