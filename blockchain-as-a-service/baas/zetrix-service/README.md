---
description: Zetrix API services.
---

# Zetrix Service

A Zetrix BaaS API allows developers to interact with the underlying blockchain network and utilize its features and functionality through a standardized interface. Some common capabilities provided by a BaaS API include:

1. **Blockchain Account Management:** APIs to create, configure, and manage account API provides programmatic access on a blockchain network.
2. **Smart Contract Deployment and Execution**: APIs to deploy smart contracts to the blockchain network and invoke their functions. This includes functions for compiling, deploying, and interacting with smart contracts.
3. **Crosschain Blockchain** APIs: Typically use a set of methods to interact with smart contracts and other systems across different blockchain networks.
4. **Transaction and Data Management:** APIs for creating, signing, and submitting transactions to the blockchain network. They may also provide functionality for querying transaction status, retrieving transaction history, and managing data on the blockchain.
5. **Verifiable Credential :** APIs for managing user identities, permissions, and access controls on the blockchain network. Zetrix Verifiable Credential Certs and its API allow you to generate and digitally sign Verifiable Credentials.

The **endpoints for Zetrix BaaS API** for both mainnet and testnet are defined as

1. https://test-api.zetrix.com (Zetrix Testnet)
2. https://api.zetrix.com (Zetrix Mainnet)

In a nutshell, developer has to request for the access token to be included in the API call. The access token has to be provided in Authorization header.

> Headers
>
> Authorization : Bearer \<ACCESS\_TOKEN>
