# Change Owner

The **Change Owner** feature allows the multisig account to transfer ownership of a smart contract to another authorized address. This is typically used during organizational restructuring, admin rotation, leadership changes, or when shifting operational responsibility to a new controlling entity. Like all critical operations, this function is fully secured by the multisignature approval mechanism to ensure trusted and collective decision-making.

***

<figure><img src="../../../../.gitbook/assets/image (26).png" alt=""><figcaption></figcaption></figure>

#### **1️⃣ Select Change Owner**

Select the **Change Owner** option under Transaction Type.

By choosing this option, you are preparing a governance-level transaction that will reassign contract ownership. This is a highly sensitive action because the contract owner typically holds privileged permissions such as configuration control, upgrade authority, or administrative capabilities.

Common use scenarios include:

* Business or team restructuring
* Transferring ownership to another department or administrator
* Security rotation or access management changes

***

#### **2️⃣ Enter Contract Address**

Provide the **contract address** of the smart contract whose ownership you intend to transfer.

This ensures the system correctly identifies which contract will be affected. The address must be accurate, as the change will be applied directly to the referenced contract.

This step ensures:

* Correct ownership target
* Prevention of accidental reassignment
* Transparency and traceability

***

#### **3️⃣ Enter New Owner Address**

Input the **new owner’s wallet address** that will receive contract ownership rights.

This address will become the new contract administrator immediately after execution. It is recommended to verify the address carefully before submission to avoid misdirection of ownership.

This step:

* Assigns new administrative authority
* Ensures controlled ownership transition
* Supports governance alignment

***

#### **4️⃣ Add Remarks (Optional)**

You may include a short remark or description explaining the reason for the ownership change.

Remarks are useful for:

* Audit reference
* Governance documentation
* Organizational approval clarity

Examples:

* “Transfer to new admin team”
* “Ownership migration to security ops”

***

#### **5️⃣ Create Transaction**

Click **Create Transaction** to generate the ownership change proposal.

Important notes:

* The ownership transfer does NOT happen immediately
* A **proposal** is created first
* Other authorized signers must approve or reject
* Ownership is updated only after required approvals are collected

This ensures strong, policy-driven governance control.

***

#### **6️⃣ Confirm Transaction Creation**

A confirmation prompt will appear asking you to verify your intent.

Click **Create Now** to finalize.

Once confirmed:

* The proposal is officially recorded
* It appears in Pending Proposals
* Signers can review and approve
* After approvals meet the threshold → Ownership transfer executes
* The new owner becomes active

This final confirmation prevents accidental submissions and reinforces secure operational decisions.
