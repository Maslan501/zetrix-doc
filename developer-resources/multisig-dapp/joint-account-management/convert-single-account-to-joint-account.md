# Convert Single Account to Joint Account

### **Purpose**

This feature allows users to upgrade an existing **single-owner Zetrix account** into a **secure multisignature (multisig) account**. Instead of being controlled by only one private key, the account is transformed into a jointly managed wallet where multiple authorized signers share responsibility and approval power.

By converting to multisig, the account adopts structured governance rules, weighted approvals, and a signature threshold mechanism. This significantly enhances security, reduces dependency on a single individual, and ensures accountability in financial and operational transactions.

### Why Use This?

#### **Secure previously single-owner wallets**

Single-controlled wallets pose several risks:

* If the private key is compromised, funds can be stolen
* If the owner loses access, control is permanently lost
* One mistake can cause irreversible loss

Converting to multisig spreads responsibility across multiple trusted parties, creating a strong security shield.

#### **Prevent unauthorized transfers**

With multisig:

* No single person can move funds alone
* Transactions require multiple approvals
* Malicious or accidental transfers can be blocked

This is critical for:

* Corporate treasuries
* Organization funds
* Shared investment accounts
* Government or institutional wallets

#### **Enable shared governance**

Multisig supports structured decision-making:

* Multiple stakeholders participate
* Approval policies can match organizational structures
* Weighted voting ensures fair control distribution

This aligns with:

* Business governance models
* DAO operations
* Joint ownership arrangements
* Compliance and audit requirements

### Flow

1️⃣ **Select “Convert Existing Account”**

Start the process by choosing the option to convert a currently single-controlled wallet into a joint account.&#x20;

<figure><img src="../../../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

2️⃣**Enter the New Joint Account Details**

You will be required to configure key governance parameters:

* **New Joint Account Name**\
  A meaningful label to identify the upgraded multisig account in the dashboard.

<figure><img src="../../../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

* **Signers**\
  Add the list of authorized wallet addresses that will jointly control the account.
* **Weight Rules**\
  Assign approval weight to each signer.\
  Higher weight = greater approval influence.

You will also typically configure a **threshold**, meaning:

* How much total approval weight is required to approve transactions.

<figure><img src="../../../.gitbook/assets/image (12).png" alt=""><figcaption></figcaption></figure>

3️⃣**Confirm and submit**

Review all configured details carefully:

* Signers list correctness
* Weight assignment accuracy
* Threshold logic validity

Once confirmed, submit the conversion request.

This triggers a blockchain transaction to:

* Replace single-owner design
* Activate multisig governance
* Apply new security structure

<figure><img src="../../../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>
