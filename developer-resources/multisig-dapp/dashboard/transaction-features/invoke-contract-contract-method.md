# Invoke Contract - Contract Method

This section allows users to invoke a smart contract from the multisig account. It supports sending ZTP20 tokens or calling any supported contract method, while still following the multisignature approval and execution workflow.

***

<figure><img src="../../../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

#### **1️⃣ Select Invoke Contract**

Choose the **Invoke Contract** option under Transaction Type.\
This tells the system that instead of sending native Zetrix, you are performing a smart contract interaction. This is commonly used for token transfers, contract execution functions, or system contract interactions that require multisig approval.



***

#### **2️⃣ Choose Contract Method Type**

Select the method type to specify how the contract will be invoked:

* **ZTP20 (Token Transfer)** – used when sending ZTP20 tokens
* **Others** – used for custom contract interactions beyond token transfers

This selection helps the system identify what kind of transaction structure is required.

***

#### **3️⃣ Enter Contract Method**

Provide the **method name** that will be executed in the contract.\
For example, `submitTransaction` or any valid function supported by the deployed contract.

This field defines exactly which function the contract should run once the transaction is executed after approval.

***

#### **4️⃣ Enter JSON Input**

Insert the required **JSON payload / parameters** for the contract call.\
This includes all necessary execution data such as:

* Contract parameters
* Transaction values
* Execution arguments

The JSON input ensures the blockchain understands precisely what action must be performed.

***

#### **5️⃣ Create Transaction**

Click **Create Transaction** to submit the proposal.

Important notes:

* The transaction is NOT executed immediately
* It is created as a **proposal**
* Other authorized signers must review and approve it
* Execution will occur only when the required approval threshold is met

This ensures strong security and collective decision control.

***

#### **6️⃣ Confirm Transaction Creation**

A confirmation dialog will appear to verify intent.

Select **Create Now** to finalize transaction creation.

Once confirmed:

* The proposal is recorded
* It appears in Pending Proposals
* Signers can approve or reject
* Execution happens only after reaching required approvals

This final confirmation step prevents accidental submissions and ensures user awareness.
