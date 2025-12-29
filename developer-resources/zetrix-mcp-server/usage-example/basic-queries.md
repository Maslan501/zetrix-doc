# Basic Queries

### **Check Node Health**

**You:**

```
Is the Zetrix mainnet node online and healthy?
```

**Claude will:**

* Use `zetrix_check_health`
* Return health status, network info, and timestamp

**Expected Response:**

```
Yes, the Zetrix mainnet node is healthy and responding at https://node.zetrix.com
```

***

### **Get Latest Block Information**

**You:**

```
What's the latest block on Zetrix? Show me the block number, hash, and transaction count.
```

**Claude will:**

* Use `zetrix_get_latest_block`
* Parse and format the response

**Expected Response:**

```
The latest block is #1,234,567 with hash 0xabc123...
It contains 15 transactions and was created at [timestamp].
```

***
