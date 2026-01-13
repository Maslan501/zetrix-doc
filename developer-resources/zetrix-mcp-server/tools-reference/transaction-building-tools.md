# Transaction Building Tools

### Transaction Blob(**`zetrix_get_transaction_blob`)**

Serialize transaction data to hex format.

**Purpose:** Convert transaction objects into blockchain-ready hex format.

**Process:** JSON transaction → Protocol Buffer serialization → Hex encoding

**Use Cases:**

* Custom transaction signing
* Offline transaction preparation

***

### Submit Signed Transaction(**`zetrix_submit_transaction`)**

Submit signed transaction to blockchain.

**Purpose:** Broadcast signed transaction for network processing.

**Input:** Signed transaction blob (hex format)

**Returns:** Transaction hash if successful

**Validation:** Checks signature, nonce, and fee before submission

**Finality:** Typically confirmed in 1-5 seconds

***

### Test Transaction Execution(**`zetrix_test_transaction`)**

Evaluate transaction fees without submission.

**Purpose:** Estimate gas costs and validate transaction structure.

**Process:** Simulates transaction execution without state changes

**Cost:** Free (no actual transaction)

**Use Cases:**

* Fee calculation
* Transaction validation
* Error detection

***

### Read-Only Smart Contract Call(**`zetrix_call_contract`)**

Call smart contract in sandbox environment.

**Purpose:** Test contract functions in isolated environment.

**Environment:** Sandboxed execution, no state changes

**Returns:** Function return value, execution logs

**Cost:** Free, no gas required

***
