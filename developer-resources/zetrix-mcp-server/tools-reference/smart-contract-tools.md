# Smart Contract Tools

Based on [zetrix-contract-development-tool](https://github.com/armmarov/zetrix-contract-development-tool).

### Initialize Smart Contract Development Environment(**`zetrix_contract_init_dev_environment`)**

Initialize contract development project.

**Purpose:** Bootstrap complete smart contract development environment.

**Creates:**

* `contracts/` - Main contract source files
* `contracts/library/` - Reusable modules
* `contracts/specs/` - Test specifications
* `test/` - Integration tests
* `scripts/` - Build, merge, deployment scripts
* `.env` - Configuration file

**Includes:**

* BasicOperation class for common patterns
* Test framework with TEST\_INVOKE and TEST\_QUERY
* Example contracts (ZTP20, ZTP721, ZTP1155)
* Deployment utilities

**Time to Setup:** \~30 seconds

***

### Generate Advanced Smart Contract Template(**`zetrix_contract_generate_advanced`)**

Generate multi-class contracts with tests.

**Purpose:** Automatically scaffold complex contracts with OOP patterns.

**Capabilities:**

* Multi-class architecture with inheritance
* Interface definitions
* Library modules
* Comprehensive test specifications

**Generated Files:**

* Main contract class with init/main/query functions
* Library modules for reuse
* Test specs for validation
* Integration test templates

**Patterns Supported:**

* ES5 class simulation with function constructors
* Prototype-based inheritance
* Module pattern for encapsulation

**Customization:** Template-based generation, fully customizable

**Time to Generate:** 2-5 seconds for full project

***

### Retrieve Built-In Chain Functions(**`zetrix_contract_get_chain_functions`)**

Chain object function documentation.

**Purpose:** Complete reference for all Chain.\* built-in functions.

**Categories:**

* Storage Operations: `store()`, `load()`, `del()`
* Account Queries: `getBalance()`, `getAccountMetadata()`
* Transactions: `payCoin()`, `payAsset()`, `issueAsset()`
* Contract Operations: `contractCall()`, `contractQuery()`, `delegateCall()`
* Blockchain Queries: `getBlockHash()`, `getBlockHeight()`, `getBlockTimestamp()`

**Documentation Includes:**

* Function signature with parameter types
* Detailed parameter descriptions
* Return value types and formats
* Usage examples with real scenarios
* Gas cost estimation
* Common pitfalls and best practices

***

### Retrieve Utility Helper Functions(**`zetrix_contract_get_utils_functions`)**

Utils object function documentation.

**Purpose:** Reference for all Utils.\* helper functions.

**Categories:**

* String/Number Operations: `stoI64Check()`, `int64Add/Sub/Mul/Div()`
* Cryptography: `sha256()`, `ecVerify()`
* Validation: `addressCheck()`, `typeOf()`
* Conversion: `jsonStringify()`, `jsonParse()`
* Array Operations: `arrayIndexOf()`, `arrayContains()`

**String Math Functions:**

* `int64Compare()` - Compare large numbers as strings
* `int64Add/Sub/Mul/Div()` - Arithmetic without precision loss
* `int64Mod()` - Modulo operation for large numbers

**Crypto Functions:**

* `sha256(msg, times)` - Hash with configurable iterations
* `ecVerify(msg, publicKey, signature)` - Ed25519 signature verification

***

### Get Smart Contract Structure Guide(**`zetrix_contract_get_structure_guide`)**

ES5 contract patterns and inheritance.

**Purpose:** Guide to writing contracts in ES5 JavaScript (no ES6+ features).

**Covers:**

* Function constructors for class simulation
* Prototype chain for inheritance
* `this` keyword binding in different contexts
* Closure patterns for private variables
* Module pattern for organization

**Restrictions:**

* ❌ No arrow functions
* ❌ No let/const
* ❌ No template literals
* ❌ No class keyword

***

### Retrieve Token Standard Specification(**`zetrix_contract_get_token_standard`)**

Token standards (ZTP20, ZTP721, ZTP1155).

**Purpose:** Complete specifications for Zetrix token standards.

**ZTP20 (Fungible Tokens):**

* Interface: `name()`, `symbol()`, `decimals()`, `totalSupply()`
* Transfer: `transfer(to, value)`, `transferFrom(from, to, value)`
* Approval: `approve(spender, value)`, `allowance(owner, spender)`
* Events: Transfer, Approval
* Use Cases: Currencies, utility tokens, reward points

**ZTP721 (Non-Fungible Tokens):**

* Interface: `name()`, `symbol()`, `tokenURI(tokenId)`
* Ownership: `balanceOf(owner)`, `ownerOf(tokenId)`
* Transfer: `transferFrom(from, to, tokenId)`, `safeTransferFrom()`
* Approval: `approve(to, tokenId)`, `setApprovalForAll(operator, approved)`
* Use Cases: Digital art, collectibles, unique assets

**ZTP1155 (Multi-Token):**

* Interface: `balanceOf(account, id)`, `balanceOfBatch()`
* Transfer: `safeTransferFrom()`, `safeBatchTransferFrom()`
* Approval: `setApprovalForAll(operator, approved)`
* Events: TransferSingle, TransferBatch, ApprovalForAll
* Use Cases: Gaming items, mixed token types, efficient multi-token management

***

### Get Smart Contract Testing Guide(**`zetrix_contract_get_testing_guide`)**

Testing framework documentation.

**Purpose:** Guide to testing smart contracts before deployment.

**Test Types:**

* Unit Tests: Test individual functions in isolation
* Integration Tests: Test contract interactions
* Spec Tests: TEST\_INVOKE and TEST\_QUERY for quick validation

**TEST\_INVOKE Usage:**

```javascript
// Test state-changing function
TEST_INVOKE('transfer', 'recipientAddress', '1000');
// Verifies function executes without errors
```

**TEST\_QUERY Usage:**

```javascript
// Test read-only function
TEST_QUERY('balanceOf', 'userAddress');
// Returns value without state change
```

**Testing Framework Features:**

* Automatic contract compilation and merging
* Sandbox execution environment
* Gas usage reporting
* State snapshot and rollback
* Mock accounts and balances

**Best Practices:**

* Test all edge cases and error conditions
* Verify access controls and permissions
* Check arithmetic overflow/underflow
* Validate input sanitization
* Test reentrancy protection

***
