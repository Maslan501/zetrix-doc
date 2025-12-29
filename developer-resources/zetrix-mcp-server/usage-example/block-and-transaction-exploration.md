# Block & Transaction Exploration

### **Get Specific Block**

**You:**

```
Show me details for block number 12345
```

**Claude will:**

* Use `zetrix_get_block` with blockNumber: 12345
* Display block header and transaction info

***

### **Get Transaction Details**

**You:**

```
What are the details of transaction 0xabc123def456...?
```

**Claude will:**

* Use `zetrix_get_transaction`
* Show source address, operations, status, fees

***

### **Explore Recent Transaction History**

**You:**

```
Show me recent transactions from block 12345
```

**Claude will:**

* Use `zetrix_get_transaction_history` with ledgerSeq: 12345
* List all transactions in that block

***
