# HTTP RPC Tools

Direct HTTP RPC interface to the Zetrix blockchain.

### **Account Operations**

### Retrieve Account Details (`zetrix_get_account`)

Get complete account information including balance, nonce, assets, and metadata.

**Purpose:** Retrieve the full state of any Zetrix account in a single request.

**Parameters:**

* `address` (string, required): Zetrix account address (ZTX...)

**Returns:**

```json
{
  "address": "ZTX3Ta7d4GyAXD41H2kFCTd2eXhDesM83rvC3",
  "balance": "1000000000",
  "nonce": "42",
  "assets": [...],
  "metadata": [...]
}
```

**Use Cases:**

* Dashboard data loading
* Account verification
* Asset inventory
* Full account state queries

**Performance:** \~100-200ms per query

**Example:**

```
Ask Claude: "Get full account details for ZTX3Ta7d4GyAXD41H2kFCTd2eXhDesM83rvC3"
```

***

### Retrieve Base Account Details(**`zetrix_get_account_base`)**

Get basic account information without assets and metadata (lightweight query).

**Purpose:** Quick account info retrieval when you don't need asset/metadata details.

**Parameters:**

* `address` (string, required): Zetrix account address

**Returns:**

```json
{
  "address": "ZTX3Ta7d4GyAXD41H2kFCTd2eXhDesM83rvC3",
  "balance": "1000000000",
  "nonce": "42"
}
```

**Use Cases:**

* Balance checks
* Nonce retrieval for transactions
* Quick account validation

**Performance:** \~50-100ms (faster than full account query)

**Recommendation:** Use this instead of `get_account` when you only need basic info.

***

### **Retrieve Account Asset(`zetrix_get_account_assets`)**

Get asset holdings for an account (tokens and custom assets).

**Purpose:** Query all tokens and custom assets held by an account.

**Parameters:**

* `address` (string, required): Zetrix account address

**Returns:**

```json
{
  "assets": [
    {
      "key": {
        "issuer": "ZTX...",
        "code": "USD"
      },
      "amount": "1000000"
    }
  ]
}
```

**Use Cases:**

* Token portfolio display
* Asset inventory
* Wallet balances

**Note:** Only returns assets with non-zero balance.

***

### **Retrieve Account Metadata(`zetrix_get_account_metadata`)**

Get metadata associated with an account (key-value pairs).

**Purpose:** Retrieve metadata stored in account state.

**Parameters:**

* `address` (string, required): Zetrix account address

**Returns:**

```json
{
  "metadata": [
    {
      "key": "name",
      "value": "My Account"
    }
  ]
}
```

**Limits:**

* Keys: Max 1024 bytes
* Values: Max 256KB per entry

***

### **Retrieve Account Balance(`zetrix_get_balance`)**

Get ZETRIX balance (returned in both ZETA and ZETRIX units).

**Purpose:** Simple balance query with automatic unit conversion.

**Parameters:**

* `address` (string, required): Zetrix account address

**Returns:**

```json
{
  "zeta": "1234567890",
  "zetrix": "1234.567890"
}
```

**Conversion:** 1 ZETRIX = 1,000,000 ZETA

**Example:**

```
Ask Claude: "What's the balance of ZTX3Ta7d4GyAXD41H2kFCTd2eXhDesM83rvC3?"
```

***

### **Retrieve Account Nonce(`zetrix_get_nonce`)**

Get account nonce for transaction sequencing.

**Purpose:** Retrieve current sequence number needed for transaction construction.

**Parameters:**

* `address` (string, required): Zetrix account address

**Returns:**

```json
{
  "nonce": "42"
}
```

**Important:** Failed transactions still consume nonce to prevent replay attacks.

***

### **Blockchain Data**

### **Retrieve Block(`zetrix_get_block`)**

Get information about a specific block by height.

**Purpose:** Explore historical blocks and their contents.

**Parameters:**

* `blockNumber` (number, required): Block height

**Returns:**

```json
{
  "header": {...},
  "transactions": [...]
}
```

**Data Size:** \~5-50KB depending on transaction count

**Performance:** \~200-500ms

***

### **Retrieve Latest Block(`zetrix_get_latest_block`)**

Get the latest block information.

**Purpose:** Monitor real-time blockchain activity and current state.

**Returns:**

```json
{
  "block_number": 12345678,
  "tx_count": 42,
  "timestamp": 1701234567890000
}
```

**Recommended:** Poll this endpoint at maximum once per second.

***

### **Retrive Ledger Information(`zetrix_get_ledger`)**

Get ledger information with validator details.

**Purpose:** Access consensus and validator information.

**Returns:**

* Validator addresses
* Stakes
* Proposal statistics
* Uptime metrics

***

### **Transactions**

### **Retrieve Transaction(`zetrix_get_transaction`)**

Get transaction details by hash.

**Purpose:** Look up any transaction by its unique identifier.

**Parameters:**

* `hash` (string, required): Transaction hash

**Returns:**

```json
{
  "hash": "0xabc123...",
  "ledger_seq": 12345,
  "transaction": {...}
}
```

**Note:** Returns error if transaction doesn't exist or is too old (pruned).

***

### **Retrieve Transaction History(`zetrix_get_transaction_history`)**

Get completed transaction records.

**Purpose:** Retrieve paginated transaction history for any account.

**Parameters:**

* `address` (string, required): Account address
* `ledgerSeq` (number, optional): Starting block number

**Performance:** \~200-500ms for 100 transactions

***

### **Retrieve Transaction Cache(`zetrix_get_transaction_cache`)**

Get pending transactions.

**Purpose:** View transactions submitted but not yet confirmed.

**Returns:** List of pending transactions

**Lifetime:** Transactions stay in cache for 1-5 minutes before confirmation or expiry.

***

### **Retrive Node Health(`zetrix_check_health`)**

Check node health status.

**Purpose:** Verify RPC node is operational and responsive.

**Returns:**

```json
{
  "status": "healthy",
  "network": "mainnet",
  "nodeUrl": "https://node.zetrix.com"
}
```

**Recommendation:** Call this before starting batch operations.

***

### **Execute Multi Query(`zetrix_multi_query`)**

Execute multiple API queries simultaneously.

**Purpose:** Batch multiple blockchain queries efficiently.

**Advantages:**

* Parallel execution
* Reduced latency
* Single response

**Use Cases:**

* Dashboard data loading
* Account aggregation
* Multi-address balance checks

**Limits:** Maximum 10 queries per batch

**Performance:** 3-5x faster than sequential queries

***
