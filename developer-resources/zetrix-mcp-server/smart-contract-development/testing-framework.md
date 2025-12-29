# Testing Framework

### **Initialize Development Environment**

**Ask Claude:**

```
Initialize a new Zetrix smart contract development environment
```

**Claude will:**

* Use `zetrix_contract_init_dev_environment`
* Create folder structure:
  * `contracts/` - Main contract source files
  * `contracts/library/` - Reusable modules
  * `contracts/specs/` - Test specifications
  * `test/` - Integration tests
  * `scripts/` - Build, merge, deployment scripts
  * `.env` - Configuration file

***

### **TEST\_INVOKE Function**

Execute transactions and validate results:

```javascript
TEST_INVOKE(
    "Test description",
    contractAddress,
    txInitiator,
    {
        method: "methodName",
        params: {
            param1: "value1",
            param2: "value2"
        }
    },
    TEST_RESULT.SUCCESS  // or TEST_RESULT.FAILED
);
```

**Example:**

```javascript
TEST_INVOKE(
    "Transfer tokens from Alice to Bob",
    contractAddress,
    aliceAddress,
    {
        method: "transfer",
        params: {
            to: bobAddress,
            amount: "1000"
        }
    },
    TEST_RESULT.SUCCESS
);
```

***

### **TEST\_QUERY Function**

Query contract state and validate:

```javascript
TEST_QUERY(
    "Test description",
    contractAddress,
    {
        method: "methodName",
        params: {
            param1: "value1"
        }
    },
    TEST_CONDITION.EQUALS,  // or GREATER_THAN, LESSER_THAN, CONTAINS
    expectedValue,
    ["result", "field", "path"]  // JSON path to value
);
```

**Example:**

```javascript
TEST_QUERY(
    "Check Bob's balance is 1000",
    contractAddress,
    {
        method: "balanceOf",
        params: {
            address: bobAddress
        }
    },
    TEST_CONDITION.EQUALS,
    "1000",
    ["result", "balance"]
);
```

***
