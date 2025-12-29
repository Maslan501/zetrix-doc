# Built-in Functions Reference

### **Chain Object**

The `Chain` object provides blockchain interaction capabilities within smart contracts.

**Metadata Management**

```javascript
// Store data in contract storage
Chain.store(key, value);

// Retrieve data from contract storage
const data = Chain.load(key);

// Delete data from contract storage
Chain.del(key);
```

**Example:**

```javascript
// Store user balance
Chain.store('balance_' + userAddress, '1000000');

// Retrieve user balance
const balance = Chain.load('balance_' + userAddress);
```

***

### **Account Query Functions**

```javascript
// Get account balance (returns string)
const balance = Chain.getBalance(address);

// Get account metadata
const metadata = Chain.getAccountMetadata(address, key);

// Get account assets
const asset = Chain.getAccountAsset(address, assetKey);

// Get account privileges
const privilege = Chain.getAccountPrivilege(address);

// Get contract properties
const props = Chain.getContractProperty(address);
```

***

### **Transaction Functions**

```javascript
// Transfer ZETRIX coins (amount in ZETA)
Chain.payCoin(toAddress, amount, input, metadata);

// Transfer assets
Chain.payAsset(toAddress, issuer, code, amount, input, metadata);

// Issue new assets
Chain.issueAsset(code, amount);
```

**Example:**

```javascript
// Transfer 1 ZETRIX (1000000 ZETA)
Chain.payCoin(recipientAddress, '1000000');

// Transfer custom asset
Chain.payAsset(recipientAddress, issuerAddress, 'MYTOKEN', '100');
```

***

### **Contract Operations**

```javascript
// Call another contract
Chain.contractCall(contractAddress, asset, amount, input);

// Query another contract (read-only)
const result = Chain.contractQuery(contractAddress, input);

// Create/deploy a new contract
Chain.contractCreate(balance, type, code, input);

// Delegate call (execute code in current context)
Chain.delegateCall(contractAddress, input);

// Delegate query (query in current context)
const result = Chain.delegateQuery(contractAddress, input);
```

***

### **Logging and Events**

```javascript
// Log transaction events (generates on-chain log)
Chain.tlog(topic, arg1, arg2, arg3, arg4, arg5);
```

**Example:**

```javascript
// Log token transfer event
Chain.tlog('Transfer', senderAddress, receiverAddress, amount);
```

***

### **Built-in Objects**

```javascript
// Current block information
const blockNumber = Chain.block.number;
const blockTimestamp = Chain.block.timestamp;

// Transaction information
const initiator = Chain.tx.initiator;
const sender = Chain.tx.sender;
const gasPrice = Chain.tx.gasPrice;
const txHash = Chain.tx.hash;
const feeLimit = Chain.tx.feeLimit;

// Message information
const msgInitiator = Chain.msg.initiator;
const msgSender = Chain.msg.sender;
const coinAmount = Chain.msg.coinAmount;
const asset = Chain.msg.asset;
const nonce = Chain.msg.nonce;
const operationIndex = Chain.msg.operationIndex;

// Current contract address
const contractAddr = Chain.thisAddress;
```

***

### **Utils Object**

The `Utils` object provides cryptographic and mathematical utilities.

**256-bit Integer Operations**

```javascript
// Compare two 256-bit integers (returns 1, 0, or -1)
const cmp = Utils.int256Compare(x, y);

// Arithmetic operations (all return strings)
const sum = Utils.int256Add(x, y);
const diff = Utils.int256Sub(x, y);
const product = Utils.int256Mul(x, y);
const quotient = Utils.int256Div(x, y);
const remainder = Utils.int256Mod(x, y);
```

**Example:**

```javascript
// Calculate total supply
const total = Utils.int256Add('1000000', '500000'); // "1500000"

// Check if balance is sufficient
if (Utils.int256Compare(balance, amount) >= 0) {
    // Sufficient balance
}
```

***

### **64-bit Integer Operations**

```javascript
const cmp = Utils.int64Compare(x, y);
const sum = Utils.int64Add(x, y);
const diff = Utils.int64Sub(x, y);
const product = Utils.int64Mul(x, y);
const quotient = Utils.int64Div(x, y);
const remainder = Utils.int64Mod(x, y);
```

***

### **Cryptographic Functions**

```javascript
// SHA-256 hashing (returns base16 encoded hash)
const hash = Utils.sha256(data, dataType);

// Verify ECDSA signature
const isValid = Utils.ecVerify(signedData, publicKey, blobData, blobDataType);

// Convert public key to address
const address = Utils.toAddress(publicKey);
```

**Example:**

```javascript
// Hash message
const messageHash = Utils.sha256('Hello World', 1);

// Verify signature
if (Utils.ecVerify(signature, publicKey, message, 1)) {
    // Signature is valid
}
```

***

### **Validation Functions**

```javascript
// Validate address format
const isValidAddr = Utils.addressCheck(address);

// Validate 64-bit string number
const isValid64 = Utils.stoI64Check(strNumber);

// Validate 256-bit string number
const isValid256 = Utils.stoI256Check(strNumber);
```

***

### **Utility Functions**

```javascript
// Output log (trace level)
Utils.log('Debug info');

// Assert condition (throws if false)
Utils.assert(condition, 'Error message');

// Convert to base unit (multiply by 10^6)
const baseUnit = Utils.toBaseUnit(value);

// Convert hex to decimal
const decimal = Utils.hexToDec(strHex);
```

**Example:**

```javascript
// Validate input
Utils.assert(Utils.addressCheck(recipient), 'Invalid recipient address');

// Convert 1 ZETRIX to ZETA
const microZTX = Utils.toBaseUnit('1'); // "1000000"
```

***
