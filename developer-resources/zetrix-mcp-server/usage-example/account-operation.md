# Account Operation

### **Check Account Balance**

**You:**

```
What's the ZTX balance for address ZTX3Ta7d4GyAXD41H2kFCTd2eXhDesM83rvC3?
```

**Claude will:**

* Use `zetrix_get_balance`
* Convert from ZETA to ZETRIX

**Expected Response:**

```
The account ZTX3Ta7d4GyAXD41H2kFCTd2eXhDesM83rvC3 has:
- Balance: 1,000,000,000 ZETA
- Equivalent: 1,000.000000 ZETRIX
```

***

### **Get Complete Account Information**

**You:**

```
Show me all information for account ZTX3Ta7d4GyAXD41H2kFCTd2eXhDesM83rvC3 including assets and metadata
```

**Claude will:**

* Use `zetrix_get_account`
* Display balance, nonce, assets, and metadata

**Expected Response:**

```
Account Details:
- Address: ZTX3Ta7d4GyAXD41H2kFCTd2eXhDesM83rvC3
- Balance: 1,000.000000 ZETRIX
- Nonce: 42
- Assets: [list of assets if any]
- Metadata: [list of metadata if any]
```

***

### **Generate Test Keypair**

**You:**

```
Generate a new Zetrix keypair for testing purposes
```

**Claude will:**

* Use `zetrix_create_keypair` or `zetrix_crypto_generate_keypair`
* Return address, public key, and private key

**⚠️ Warning:** Only for testing! Never use these keys on mainnet with real funds.

***
