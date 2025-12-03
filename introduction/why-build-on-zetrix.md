# Why Build on Zetrix

## Safe and Efficient Consensus Algorithm

Zetrix’s consensus mechanism is a hybrid model that integrates Delegated Proof of Stake (DPoS) with an advanced form of Byzantine Fault Tolerance called zBFT. This architecture is carefully designed to deliver both safety and efficiency, addressing the need for fast transaction processing, robust fault tolerance, and environmental sustainability. By combining these two layers, Zetrix provides an optimal consensus model suitable for high-throughput and compliance-sensitive applications across both the enterprise and public sectors.

At the foundation of this model is DPoS, a voting-based mechanism where Zetrix token holders delegate their tokens to elect a limited set of validator nodes. These elected validators are responsible for producing blocks in a fixed, cyclical order. This significantly reduces the computational overhead seen in traditional Proof-of-Work systems, allowing Zetrix to operate at high transaction speeds with minimal energy consumption. The dynamic nature of validator selection also enhances decentralisation and introduces a layer of governance, as voters can replace non-performing or malicious validators through staking smart contracts.

Layered on top of DPoS is the Zetrix Byzantine Fault Tolerance (zBFT) protocol. This component ensures strong consistency and resilience within the validator node set. For every block, a random selection of 21 validators is made, and these validators reach consensus through a secure message-passing process. Unlike traditional BFT protocols that struggle with communication overhead, zBFT optimises this process by reducing the message complexity from quadratic to linear scale.

![](<../.gitbook/assets/image (15) (1).png>)

## Parallel and Diverse Multi-Chain Sharding

Traditional blockchain systems often rely on a single-chain architecture, where all transactions and data are handled through a centralised, unified ledger. This design creates performance bottlenecks, as the entire network's efficiency depends on the processing capacity of individual nodes. As transaction volumes grow, such systems encounter scalability and throughput limitations.

Additionally, most of these systems store data directly on-chain, which significantly increases storage and computational overhead. This further constrains performance and makes it challenging to support high-demand enterprise applications, especially those requiring real-time processing, large data throughput, or complex logic execution.

Zetrix addresses these challenges through a multi-chain sharding architecture, enabling the blockchain to scale horizontally. This approach segments data and transaction processing across multiple independent sub-chains, each tailored to specific business functions or application domains. These sub-chains operate in parallel, significantly increasing the system’s overall throughput and responsiveness.

The underlying framework is described as a “two-layer capacity expansion model.” The main chain manages governance, validation, and security coordination, while the sub-chains handle specialised computation and data storage tasks. Each sub-chain inherits the security and trust guarantees of the main chain but operates independently, ensuring that congestion or data growth on one chain does not impact others. This results in true resource isolation and flexible, scalable deployment across diverse industry use cases.

![](<../.gitbook/assets/image (5) (1).png>)

## Reliable Privacy Protection and Permission Control

The Zetrix blockchain integrates advanced cryptographic technologies, including homomorphic hiding and zero-knowledge proofs, to enable non-interactive privacy protection. This ensures that transaction amounts, addresses, and metadata remain confidential. Additionally, Zetrix employs a proxy re-encryption algorithm to support authorized access to on-chain data, allowing designated parties to securely decrypt and interact with private information without exposing it to the public.

Non-interactive privacy protection in Zetrix is achieved by generating a blinding factor using the Elliptic Curve Diffie-Hellman (ECDH) algorithm. This blinding factor is calculated such that the receiver can recover it by performing an algebraic operation using their private key and the sender’s public key. However, a third party without access to either of the private keys cannot reconstruct the blinding factor. This cryptographic approach ensures that transaction details remain hidden from unauthorized observers, effectively enabling confidential transactions without direct interaction between sender and receiver.

Transaction amount hiding in Zetrix is accomplished using Pedersen commitments, which provide homomorphic encryption of transaction amounts. This means that the sum or difference of encrypted values corresponds to the encrypted result of the sum or difference of the original values. As a result, blockchain nodes can perform mathematical operations directly on encrypted data such as verifying that total inputs equal total outputs without ever revealing the actual transaction amounts. This ensures both privacy and verifiability in confidential transactions.

Transaction address hiding in Zetrix is achieved by transferring ownership through a transformed version of the recipient’s public key. For each transaction output, a unique one-time public key is generated based on the recipient’s original key using a defined transformation rule. This ensures that each output uses a different address, making it impossible for external observers to link multiple transactions to the same recipient. As a result, ownership changes occur securely while preserving address confidentiality and preventing traceability.

Ask ChatGPT

![](<../.gitbook/assets/image (12) (1).png>)

## Application Development - Friendly Smart Contract

The Zetrix blockchain features a smart contract engine built on the Chrome V8 virtual machine, offering an integrated development environment (IDE) along with multiple contract templates tailored for various application scenarios. To enhance contract security, Zetrix employs a restricted subset of JavaScript as its primary programming language, enabling developers to safely and efficiently build smart contract applications.

![](<../.gitbook/assets/image (16) (1).png>)

## Scalable Homogeneous / Heterogeneous Cross Chain Interoperability

The Zetrix blockchain utilizes an interconnected chain architecture to enable advanced cross-chain capabilities, supporting multiple interoperability models such as multi-notary consensus and trusted hardware mechanisms. Developers can build a Relay Chain using SPV (Simplified Payment Verification) to connect with external business chains and create parallel chains as needed, which are then linked to the Relay Chain via a defined cross-chain protocol. This modular structure reduces architectural complexity and offers greater flexibility, making it ideal for practical enterprise use cases. Zetrix supports both isomorphic and heterogeneous cross-chain operations, facilitating not only the transfer of digital assets but also enabling cross-chain execution of smart contracts and decentralized applications.

![](https://docs.bubi.cn/en/docs/assets/relay_chain.png)

## Legal Effect and Compliance

The Zetrix Blockchain integrates digital certificates into its network for identity authentication, supporting both software and hardware modes. Software certificates enhance operational convenience, while hardware certificates bolster security for sensitive enterprise use cases. Only users with CA-issued digital certificates can operate on the blockchain, and institutional nodes must authenticate using these certificates. By leveraging the existing PKI infrastructure, Zetrix binds all authentication data to blockchain accounts, enabling secure on-chain verification.

Zetrix supports national commercial cryptographic standards such as SM2 / SM3 / SM4, meeting the security requirements of domestic institutions, while also supporting international standards like SHA256, making it globally interoperable. Furthermore, Zetrix nodes integrate with platforms such as judicial risk control systems, Internet courts, and CFCA, enabling services like judicial deposits, electronic contracts, and real-name authentication. The ecosystem is open to developers, who can contribute their own services and participate in building a distributed business application ecosystem on the Zetrix blockchain.

## Ease of Use

Zetrix adaptors abstract API interfaces tailored for diverse business scenarios such as digital assets, traceability, and certificate storage, allowing businesses to integrate these capabilities directly. For new use cases, Zetrix can rapidly customize interfaces within its existing framework to fulfill specific business functionality requirements. To support developer adoption, Zetrix offers packaged SDKs compatible with multiple mainstream programming languages including Java, C++, Node.js, and PHP.

Currently, blockchain services follow two main models: one builds the underlying infrastructure and exposes standardized APIs for developers to integrate; the other embeds the distributed ledger into existing applications to address industry-specific challenges. As an emerging technology, blockchain must evolve by continuously adapting to real-world business needs. Zetrix accelerates this by encapsulating the distributed ledger layer, lowering the integration barrier for upper-layer applications. Through continuous usage, integration, and feedback, Zetrix optimizes its consensus algorithms and core infrastructure, making the platform increasingly aligned with commercial demands.

## Visual Operation and Maintenance

Zetrix provides comprehensive visual tools to support operation and maintenance management across its blockchain infrastructure. Through the deployment of System Monitoring Services (MonitorAgent) on each blockchain node, Zetrix enables real-time monitoring across multiple dimensions:

* Business-level metrics: Blocks, transactions, smart contracts, consensus activity
* Network-level metrics: Network connectivity, latency, throughput
* System-level metrics: CPU usage, memory consumption, disk space utilization

In addition to data visualization, the platform includes a robust logging, alarm, and notification mechanism, ensuring timely alerts and simplified troubleshooting. This integrated monitoring framework is designed to enhance the stability, reliability, and maintainability of commercial systems running on the Zetrix blockchain.

## Fund Account System

In business applications, blockchain networks typically lack real-world "money" directly on-chain. To address this, the Zetrix blockchain introduces a set of components that enable integration between blockchain accounts and traditional bank accounts. This allows for seamless settlement between digital and fiat systems. By leveraging smart contracts, the fund clearing process becomes fully automated, relying solely on on-chain digital signatures rather than a centralized system administrator. This design enhances transparency, efficiency, and trust in hybrid digital-financial ecosystems.
