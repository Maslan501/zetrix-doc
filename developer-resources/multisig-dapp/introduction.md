# Introduction

### What is a Multisig Account?

A Multisig account is a **joint-controlled blockchain account** protected by multiple signers. No one can execute actions alone unless explicitly allowed by configuration.

### Key Concepts

* **Signers** → Authorized users
* **Weights** → Each signer has a security weight value
* **Threshold** → Minimum total weight required to approve an action
* **Proposals** → Pending actions waiting for signatures
* **Execution** → Only happens when approvals reach the threshold

### This design prevents:

* Single admin abuse
* Accidental transfers
* Insider theft
* Stolen private key exploitation

### Accessing the Application

<figure><img src="../../.gitbook/assets/unknown (1).png" alt=""><figcaption></figcaption></figure>

#### Test Environment

Use this environment for development, internal evaluation, demonstration, and testing purposes.

**Test URL:**\
[https://multisig.myegdev2.com/](https://multisig.myegdev2.com/)

✔ Safe for experiments\
✔ Use test accounts\
✔ Features may update frequently

***

#### Production Environment

Use this environment for real usage, live operations, enterprise deployment, and actual fund management.

**Production URL:**\
[https://multisig.zetrix.com/#/](https://multisig.zetrix.com/#/)

✔ Stable and secure\
✔ Real blockchain transactions\
✔ Suitable for enterprise and organizational use

***

> ⚠️ **Important Notice**

* Always verify which environment you are using before performing transactions
* Never use real funds in the test environment
* Ensure signers are aware whether approvals are for test or production accounts
