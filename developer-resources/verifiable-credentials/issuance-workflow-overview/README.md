# Issuance Workflow Overview

##

<figure><img src="../../../.gitbook/assets/Zetrix-Illustration-white-bg (1).png" alt=""><figcaption></figcaption></figure>

## **Introduction**

This is an overview of the end-to-end process for issuing Verifiable Credentials (VCs) within the Zetrix ecosystem. It describes the roles, platforms, and interactions required for an Issuer to create, manage, and distribute blockchain-secured digital credentials to end-users. The workflow ensures security, trust, and interoperability across all participating systems.

## **Participants and Platforms**

### **Participants**

* **Issuer**\
  The organization responsible for managing credential issuance.
*   **System integrator (SI)**

    Entity who is responsible to build the system for the Issuer. In addition, system integrator can also be an issuer.
* **End-User**\
  The individual or entity receiving the Verifiable Credential.

### **Platforms**

* **ZPlatform (**[**https://platform.zetrix.com**](https://platform.zetrix.com)**)**\
  Subscription and API management portal.
* **Zetrix Wallet**\
  Web3 wallet supporting identity and blockchain operations.
* **Identity Platform (**[**https://identity.zetrix.com**](https://identity.zetrix.com/)**)**\
  DID (Decentralized Identifier) management and BBS+ key generation.
* **ZCert Issuer Platform (**[**https://zid-issuer.zetrix.com**](https://zid-issuer.zetrix.com/)**)**\
  Credential template management and onboarding verification.
* **Backend VC Issuance System**\
  Credential issuance engine integrating with Zetrix services.

## Process Overview

### Subscription and API Provisioning

The process begins by the SI registering on the **ZPlatform**.\
Upon successful registration:

1. The Issuer subscribes to a **VC Subscription Plan**, defining usage quotas and service access.
2. An **API Key** is generated, granting secure access to Zetrix Verifiable Credential APIs.

This step ensures the Issuer has the required permissions and infrastructure to participate in the credential issuance ecosystem.

### Wallet Creation

The Issuer proceeds to create a **Zetrix Web3 Wallet**.\
This wallet provides:

* A **Wallet Address** used as the Issuer’s blockchain identity.
* A **Private Key** used for signing operations and authentication within the ecosystem.

The wallet is an essential trust anchor for subsequent identity related operations.

For more details, please refer to the [Wallet](/broken/pages/RLu6XkDbEqkQqCQG2EWn) section.

### DID and Key Management

On the **Identity Platform**, the Issuer connects their wallet to establish their formal decentralized identity.

The following actions take place:

1. **DID Generation**\
   The system automatically creates a Decentralized Identifier (DID) linked to the Issuer’s wallet.
2. **BBS+ Key Generation**\
   A BBS+ keypair is generated for cryptographic signing.\
   This key enables:
   * Privacy-preserving credential issuance
   * Selective disclosure by end-users
   * Standards-compliant digital signatures

The DID and BBS+ keys together form the Issuer’s official cryptographic identity on Zetrix.

For more details, please refer to the [Identity Platform](generating-bbs+-key.md) section.&#x20;

### Credential Template Configuration

The Issuer accesses the **ZCert Issuer Platform** to configure VC template. This template determine the attributes and UI design of the VC.

Activities include:

1. **Onboarding Due Diligence**\
   Organizational verification to ensure the Issuer is legitimate and authorized.
2. **VC Template Creation**\
   Templates define the structure, fields, and attributes of the Verifiable Credentials.\
   Examples VC Design include:
   * General Certificates
   * Identity Card
   * Passport

Templates ensure consistency, standardization, and alignment with predefined data schemas.

For more details, please refer to the [Issuer Platform](zcert-issuer-platform.md) section.&#x20;

### VC Issuance

The Backend VC Issuance System integrates all previously prepared components:

* **API Key** (authorization)
* **VC Templates** (credential structure)
* **BBS+ Key** (cryptographic signing)
* **DID** (issuer identity)

The VC Engine performs the following functions:

1. Applies template rules and issuer metadata
2. Signs the credential using the BBS+ key
3. Stores issuance records in the internal database
4. Generates the final **Verifiable Credential** in the required format (e.g., JSON-LD, JWT)

The completed credential is then provided to the **End-User** for secure download or wallet import.

