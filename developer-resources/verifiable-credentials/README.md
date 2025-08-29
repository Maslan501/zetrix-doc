---
description: >-
  This guide will walk you through what VCs are, how Zetrix supports them, and
  how to use the Zetrix VC APIs to build decentralized identity and credential
  systems.
---

# Verifiable Credentials

## Introduction to Verifiable Credentials

#### What Are Verifiable Credentials?

**Verifiable Credentials (VCs)** are digital credentials that are cryptographically secure, privacy-respecting, and machine-verifiable. Based on the **W3C VC standard**, they are used to represent information such as ID cards, academic degrees, and licenses in a decentralized way.

A VC typically includes:

* **Issuer** (who issued it)
* **Subject** (who it's about)
* **Claims** (the data)
* **Proof** (digital signature)

#### Why Use VCs on Zetrix?

* **Trustless Verification**: No need to call the issuer to validate a VC.
* **Data Integrity**: Credentials are tamper-evident.
* **Privacy-Preserving**: Support for selective disclosure with BBS+ signatures.
* **Decentralized Identity (DID)**: Users are identified by unique, blockchain-resolved `did:zid` identifiers.



## High-Level Overview

### Roles

* **Issuer**: Entity that issues a VC.
* **Holder**: The recipient of the VC.
* **Verifier**: Entity that checks the authenticity of a VC or Verifiable Presentation (VP).

### Credential Lifecycle

1. **Template Creation**: Issuer defines the schema of the credential.
2. **VC Application**: Holder requests a VC using a defined template.
3. **VC Issuance**: Issuer fills in fixed values, signs, and issues the VC.
4. **VC Download**: Holder downloads the issued VC.
5. **VP Creation**: Holder creates a Verifiable Presentation.
6. **VP Verification**: Verifier checks the proof.
7. **VC Revocation** _(optional)_: Issuer can revoke an issued VC.

### System Components

* **TDS Smart Contract**: Manages on-chain transactions.
* **DID Resolver**: Resolves `did:zid` to metadata/public keys.
* **Blob Signing**: All transactions are submitted as signed blobs.
* **Ed25519 + BbsBls**: Signature schemes used for W3C compliance and selective disclosure.
