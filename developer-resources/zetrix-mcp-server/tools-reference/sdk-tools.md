# SDK Tools

Official Zetrix SDK integration powered by [zetrix-sdk-nodejs](https://github.com/Zetrix-Chain/zetrix-sdk-nodejs).

### Create New Zetrix Account(**`zetrix_sdk_create_account`)**

Create new Zetrix account with keys.

**Purpose:** Generate cryptographically secure new accounts offline.

**Returns:**

```json
{
  "address": "ZTXxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "privateKey": "privxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx",
  "publicKey": "b001xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"
}
```

**Algorithm:** Ed25519

**Security:** Uses cryptographically secure random number generator (CSPRNG)

**Testing:** Safe for testnet, use caution for mainnet

***

### Retrieve Account Balance(**`zetrix_sdk_get_balance`)**

Get account balance using official SDK.

**Purpose:** Query balance using SDK's built-in methods.

**Features:**

* Automatic retry
* Connection pooling
* Error handling

**Difference from HTTP:** Uses SDK's optimized request handling

**Precision:** Returns exact balance as string to avoid JavaScript number limits

***

### Check Account Activation Status(**`zetrix_sdk_is_activated`)**

Check if account is activated.

**Purpose:** Determine if address exists on blockchain.

**Returns:** Boolean (true if activated, false if not)

**Important:** Unactivated accounts cannot receive transactions.

**Activation:** Accounts activate when they receive first transaction.

**Use Cases:**

* Validate recipient before sending
* Check account existence
* Transaction validation

**Performance:** Fast query, typically <100ms

***

### Get Current Account Nonce(**`zetrix_sdk_get_nonce`)**

Get account nonce for transactions.

**Purpose:** Retrieve current nonce using SDK (alternative to HTTP RPC).

**Features:**

* Built-in retry logic
* Better error messages
* Thread-safe concurrent nonce requests

***

### Execute Read-Only Smart Contract Call(**`zetrix_sdk_call_contract`)**

Call smart contract (query only, no state change).

**Purpose:** Execute contract view functions without creating transactions.

**Cost:** Free (no gas required for queries)

**Speed:** Fast execution, typically 50-200ms

**Use Cases:**

* Read token balance
* Query contract state
* Validate parameters

**Limitation:** Cannot modify state, only read operations

***

### Invoke Smart Contract Transaction(**`zetrix_sdk_invoke_contract`)**

Invoke contract with transaction signing.

**Purpose:** Execute state-changing contract functions.

**Process:**

1. Build transaction
2. Sign with private key
3. Submit to network
4. Wait for confirmation

**Cost:** Consumes gas based on contract execution complexity

**Features:**

* Automatic gas estimation
* Nonce management
* Transaction building

**Security:** Requires private key for signing, handle securely

***
