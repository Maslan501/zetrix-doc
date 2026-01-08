# Upgrade Contract

The **Upgrade Contract** feature allows the multisig account to update or replace the existing smart contract with a newer or enhanced version. This is typically used for security improvements, feature upgrades, protocol enhancements, or bug fixes, and is fully protected by the multisignature approval process to ensure controlled governance.

***

<figure><img src="../../../../.gitbook/assets/image (25).png" alt=""><figcaption></figcaption></figure>

#### **1️⃣ Select Upgrade Contract**

Choose the **Upgrade Contract** option under Transaction Type.

This indicates that the action being initiated is not a normal fund transfer but a **smart contract upgrade operation**. By selecting this option, you are preparing to deploy new or updated contract code that will replace or enhance the existing contract logic.

This is commonly used for:

* Applying security patches
* Adding new functionalities
* Improving performance
* Fixing defects in the contract

***

#### **2️⃣ Enter Contract Address**

Provide the **contract address** of the smart contract that you intend to upgrade.

This identifies which specific deployed contract will receive the new version. The address must be accurate because the upgrade operation will directly affect whatever contract is referenced here.

This ensures:

* Correct target contract is upgraded
* No unintended contracts are affected
* Smooth upgrade execution process

***

#### **3️⃣ Insert Contract Code**

Paste or write the **new or updated contract code** into the Contract Code field.

This is the actual upgraded logic that will replace or enhance the existing smart contract. The code must be valid and properly formatted to execute successfully on the blockchain.

This step allows:

* Deployment of improved logic
* Introduction of new features
* Strengthened security mechanisms

Since this affects live contract behavior, it is important that only trusted administrators and organizations perform this action and that all signers carefully review the code before approving.

***

#### **4️⃣ Create Transaction**

Click **Create Transaction** to submit the upgrade request.

Important notes:

* The contract upgrade does NOT happen immediately
* A **proposal** is created for review
* Other signers must evaluate and approve
* Execution only occurs once the approval threshold is met

This ensures that such a critical action cannot be executed by a single person and maintains strong governance control.

***

#### **5️⃣ Confirm Transaction Creation**

A confirmation message will appear asking for final approval to create the upgrade transaction proposal.

Click **Create Now** to confirm.

Once confirmed:

* The proposal is officially created
* It appears under Pending Proposals
* Signers can approve or reject
* Upon reaching required approvals, the upgrade executes
* The new contract logic becomes active

This confirmation step ensures that users are fully aware of the impact and prevents accidental submission.
