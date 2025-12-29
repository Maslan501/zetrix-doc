# Token Standards

### **ZTP20 (Fungible Tokens)**

Standard methods for ERC20-like tokens:

```javascript
// Query methods
balanceOf(address)
totalSupply()
allowance(owner, spender)

// Transaction methods
transfer(to, amount)
approve(spender, amount)
transferFrom(from, to, amount)

// Events
Chain.tlog('Transfer', from, to, amount);
Chain.tlog('Approval', owner, spender, amount);
```

**Variants:**

* **Core** - Basic functionality
* **Burnable** - Can destroy tokens
* **Permit** - Gasless approvals
* **Pausable** - Emergency stop
* **Capped** - Maximum supply limit

***

### **ZTP721 (Non-Fungible Tokens)**

Standard methods for ERC721-like tokens:

```javascript
// Query methods
balanceOf(owner)
ownerOf(tokenId)
getApproved(tokenId)
isApprovedForAll(owner, operator)

// Transaction methods
approve(to, tokenId)
setApprovalForAll(operator, approved)
transferFrom(from, to, tokenId)
safeTransferFrom(from, to, tokenId, data)

// Events
Chain.tlog('Transfer', from, to, tokenId);
Chain.tlog('Approval', owner, approved, tokenId);
Chain.tlog('ApprovalForAll', owner, operator, approved);
```

**Use Cases:** Digital art, collectibles, unique assets

***

### **ZTP1155 (Multi-Token)**

Standard methods for ERC1155-like tokens:

```javascript
// Query methods
balanceOf(account, id)
balanceOfBatch(accounts, ids)
isApprovedForAll(account, operator)

// Transaction methods
setApprovalForAll(operator, approved)
safeTransferFrom(from, to, id, amount, data)
safeBatchTransferFrom(from, to, ids, amounts, data)

// Events
Chain.tlog('TransferSingle', operator, from, to, id, value);
Chain.tlog('TransferBatch', operator, from, to, ids, values);
Chain.tlog('ApprovalForAll', account, operator, approved);
```

**Use Cases:** Gaming items, mixed token types, efficient multi-token management

***
