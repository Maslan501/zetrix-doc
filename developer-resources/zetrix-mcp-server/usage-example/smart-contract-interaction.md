# Smart Contract Interaction

### **Call Contract Method (Read-only)**

**You:**

```
Call the contract at ZTXcontract123... with the query method and show me the result
```

**Claude will:**

* Use `zetrix_call_contract` or `zetrix_sdk_call_contract`
* Execute in sandbox (no state changes)

**Example:**

```json
{
  "contractAddress": "ZTXcontract123...",
  "input": "{\"method\":\"balanceOf\",\"params\":{\"address\":\"ZTX3Ta...\"}}",
  "sourceAddress": "ZTX3Ta7d4GyAXD41H2kFCTd2eXhDesM83rvC3"
}
```

***

### **Invoke Contract (State Change)**

**You:**

```
Invoke the contract at ZTX3Uaci... to transfer tokens. 
Use my private key privbs...
Source address: ZTX3Ta7d...
Amount: 1000000
Input: {"method":"transfer","to":"ZTX3Xb...","amount":"500"}
```

**Claude will:**

* Use `zetrix_sdk_invoke_contract`
* Build, sign, and submit the contract transaction
* Return transaction hash and result

**Expected Response:**

```json
{
  "hash": "d4de93a5491462b5fc46254652e5f084c9f7c3f815c5b7b2..."
}
```

***
