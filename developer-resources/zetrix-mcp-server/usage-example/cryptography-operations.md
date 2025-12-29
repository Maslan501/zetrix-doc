# Cryptography Operations

### **Generate New Keypair**

**You:**

```
Generate a secure new Zetrix keypair using the cryptography tools
```

**Claude will:**

* Use `zetrix_crypto_generate_keypair`
* Generate Ed25519 keypair
* Return private key, public key, and address

**Security:** Uses cryptographically secure random number generator.

***

### **Encrypt Private Key**

**You:**

```
Encrypt this private key with password "MySecurePassword123!":
privbsGZFUoRv8aXqzR8X2gYF4m1...
```

**Claude will:**

* Use `zetrix_crypto_encrypt_key`
* Apply AES-256-CBC encryption with PBKDF2
* Return encrypted key with salt and IV

**Performance:** \~100-200ms (intentionally slow for security)

***

### **Verify Signature**

**You:**

```
Verify this signature is valid:
Message: "Hello World"
Signature: abc123...
Public Key: b001...
```

**Claude will:**

* Use `zetrix_crypto_verify`
* Validate Ed25519 signature
* Return true/false

***

### Advanced Multi-Query

#### **Get Multiple Accounts at Once**

**You:**

```
Check the balances of these three addresses at once:
- ZTX3Ta7d4GyAXD41H2kFCTd2eXhDesM83rvC3
- ZTX4Ub8e5fHzBYE52J3lGDe3fyIiNf94sxwD4
- ZTX5Vc9f6gIaCZF63K4mHEf4gzJjOg05tywE5
```

**Claude will:**

* Use `zetrix_multi_query` with three getAccount requests
* Process results in parallel (3-5x faster than sequential)

***

### Natural Language Conversation Flow

The power of MCP is that you can have natural conversations:

```
You: "What's the latest block?"
Claude: "Block #1,234,567 with 15 transactions"

You: "Show me transaction details from that block"
Claude: [uses block number from context]

You: "Which address sent the most ZETRIX?"
Claude: [analyzes transactions and reports]

You: "Check that address's current balance"
Claude: [looks up the address]

You: "Monitor it for new transactions"
Claude: [subscribes via WebSocket]
```

***

### Tips for Best Results

1. **Be Specific:** Include addresses, block numbers, or transaction hashes
2. **Use Context:** Claude remembers previous results in the conversation
3. **Combine Requests:** Ask for multiple things at once for efficiency
4. **Real-time Needs:** Mention "WebSocket" or "real-time" for live monitoring
5. **Testing:** Always mention "test" or "testnet" when experimenting

***
