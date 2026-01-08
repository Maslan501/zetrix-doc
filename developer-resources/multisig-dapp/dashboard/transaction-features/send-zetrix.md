# Send Zetrix

#### **Purpose**

Enables users to transfer Zetrix native tokens from the multisig account to another blockchain address while enforcing collective approval security.

***

#### **Fields Required**

* **Recipient Address**
* **Amount**

***

#### **Security Behavior**

Transactions do **not** execute instantly.\
Instead, they are submitted as **proposals**, ensuring that:

* No individual can unilaterally transfer funds
* Transactions are evaluated and approved collectively
* Governance rules are respected

***

#### **Lifecycle**

1️⃣ Proposal Created

<figure><img src="../../../../.gitbook/assets/image (18).png" alt=""><figcaption></figcaption></figure>

\
2️⃣ Fill up details for sending token.

<figure><img src="../../../../.gitbook/assets/image (19).png" alt=""><figcaption></figcaption></figure>

\
3️⃣ Approved and sign.

<figure><img src="../../../../.gitbook/assets/image (20).png" alt=""><figcaption></figcaption></figure>

\
4️⃣ Funds is approved from initiator. To proceed the transaction next signer need to sign based on threshold.&#x20;

<figure><img src="../../../../.gitbook/assets/image (21).png" alt=""><figcaption></figcaption></figure>

5️⃣Next signer will login and approved the transaction in pending transaction. Transaction will submitted after threshold is completed.&#x20;

<figure><img src="../../../../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>

***

#### ❌ **Failure Conditions**

* Insufficient approvals collected
* Proposal rejected
* Proposal never executed

This prevents unauthorized or accidental fund movement.
