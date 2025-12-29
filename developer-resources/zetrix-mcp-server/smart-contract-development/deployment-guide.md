# Deployment Guide

### **Step 1: Generate Contract**

**Ask Claude:**

```
Generate a new ZTP20 token contract with these specs:
- Name: My Token
- Symbol: MTK
- Decimals: 6
- Total Supply: 1,000,000
```

**Claude will:**

* Use `zetrix_contract_generate_advanced`
* Create complete contract with tests
* Generate deployment scripts

***

### **Step 2: Test Locally**

**Ask Claude:**

```
Run the test specifications for my contract
```

**Claude will:**

* Execute TEST\_INVOKE and TEST\_QUERY tests
* Validate all functions work correctly
* Report gas usage

***

### **Step 3: Deploy to Testnet**

**Ask Claude:**

```
Deploy my contract to testnet using this private key: privbs...
```

**Claude will:**

* Compile and merge contract
* Use `zetrix_sdk_invoke_contract` to deploy
* Return contract address

***

### **Step 4: Verify Deployment**

**Ask Claude:**

```
Query my deployed contract at ZTX... to verify it's working
```

**Claude will:**

* Use `zetrix_sdk_call_contract`
* Query contract state
* Verify deployment success

***
