# Zetrix Official Release Notes

### Release 1: Support `int256`

**Start Date:** 18 November 2024\
**Completion Date:** 23 November 2024\
**Status:** Completed

**Description:** The inclusion of this new feature is designed to accommodate higher precision and larger numerical values, addressing current limitations and enabling Zetrix to support future scalability and advanced applications, such as high-volume transactions and complex smart contracts.

***

### Release 2: Increase transaction queue

**Completion Date:** 24 October 2025\
**Status:** Completed

**Description:** This release increases transaction queue capacity and timeout limits to handle higher load, with the transaction timeout extended to 24 hours, the total queue limit raised to 100,000, and per-account transaction limit increased to 64,000. It also updates the system to Ledger version 1002 and Chain version 1.0.3.

**Release Info**

#### Changes:

* Increased `QUEUE_TRANSACTION_TIMEOUT` to 24 hours
* Increased `queue_limit` to 100000
* Increased `queue_per_account_txs_limit` to 64000

#### Update info

* Ledger version : 1002
* Chain version : 1.0.3

***

### Release 3: Improve consensus timing

**Completion Date:** 28 October 2025\
**Status:** Completed

**Description:** This release improves consensus timing by reducing the leader node’s propose timeout and increasing the verification node’s check timeout, along with general timeout adjustments and enhanced logging. It also updates the system to Ledger version 1004 and Chain version 1.0.5.

**Release Info**

#### Changes:

* Reduce the propose timeout of the leader node and increase the check timeout of the verification node
* Increase log output
* Adjust the timeout settings

#### Update info

* Ledger version : 1004
* Chain version : 1.0.5

***

### Release 4: Improve system stability and memory management

**Completion Date:** 30 October 2025\
**Status:** Completed

**Description:** This release improves system stability and memory management by clearing cached data after block synchronization, introducing a scheduled cleanup of the transaction cache every 5 minutes, and adding debug logs for failed byte message sends. It also updates the system to Ledger version 1005 and Chain version 1.0.6.

**Release Info**

#### Changes:

1. Ensure cached data deleted after block synchronized
2. Scheduled cleanup on TX cache every 5 min
3. Added debug log when failed to send byte msg

#### Update info

* Ledger version : 1005
* Chain version : 1.0.6

***

### Release 5: Enhances performance and concurrency by enabling direct account loading from the database

**Completion Date:** 3 November 2025\
**Status:** Completed

**Description:** This testnet release enhances performance and concurrency by enabling direct account loading from the database without locks, replacing timer-based ledger sync with a dedicated thread and queue, and removing read locks from web server queries. It also improves network reliability with a transaction resend mechanism. A breaking change requires the /getAccountMetadata endpoint to include a key parameter, or it will return an error. The update moves the system to Ledger version 1006 and Chain version 1.0.7.

**Testnet Release Info**

#### Changes

1. Introduce AccountFrm::LoadAccountFromDb for direct DB access without locks
2. Replace timer-based ledger sync with dedicated thread and queue processing
3. Add transaction resend mechanism to improve network propagation
4. Remove read locks from web server query operation

#### Update info

* Ledger version : 1006
* Chain version : 1.0.7

#### Breaking change

* /getAccountMetadata MUST provide key as well, otherwise it returns error eg) /getAccountMetadata?address=\<ZTX3...>\&key=dummy

***

### Release 6: Restores mutex lock operation for block execution

**Completion Date:** 4 November 2025\
**Status:** Completed

**Description:** This release restores mutex lock operations for block execution during both synchronization and consensus to ensure proper consistency and thread safety. It updates the system to Ledger version 1007 and Chain version 1.0.8.

**Release Info**

#### Changes:

1. Restore mutex lock operations for block execution during synchronization and block execution during consensus

#### Update info

* Ledger version : 1007
* Chain version : 1.0.8

***

### Release 7: Updates the V8 engine usage and refines the calling logic of CallContract

**Completion Date:** 10 November 2025\
**Status:** Completed

**Description:** This release updates the V8 engine usage and refines the calling logic of CallContract to improve execution behavior. It also upgrades the system to Ledger version 1008 and Chain version 1.0.9.

**Release Info**

#### Changes:

1. Modify the usage method of the V8 engine
2. Adjust the calling logic of CallContract

#### Update info

* Ledger version : 1008
* Chain version : 1.0.9

***

### Release 8: MCP Server

**Completion Date:** 29 November 2025\
**Status:** Completed

**Description:** User can use it to interact with our Zetrix RPC, WebSocket and even create/deploy/test a smart contract.&#x20;

The Zetrix MCP Server is a comprehensive bridge between AI assistants (like Claude) and the Zetrix blockchain. It provides **44 specialized tools** that enable natural language interactions with blockchain operations, making blockchain technology accessible through conversational AI.

**Key Capabilities:**

* Query blockchain data (accounts, blocks, transactions)
* Execute real-time monitoring via WebSocket connections
* Generate and manage cryptographic keys
* Develop and deploy smart contracts
* Sign and submit transactions
* Interact with existing contracts

***

### Release 9: Fixes an initialization error encountered during contract queries

**Completion Date:** 16 November 2025\
**Status:** Completed

**Description:** This release fixes an initialization error encountered during contract queries, improving reliability. It updates the system to Ledger version 1009 and Chain version 1.1.0.

**Release Info**

#### Changes:

1. Fixed initialization error during contract query

#### Update info

* Ledger version : 1009
* Chain version : 1.1.0

***

### Release 10: Recompiles and fixes the initialization error during contract queries

**Completion Date:** 18 December 2025\
**Status:** Completed

**Description:** This release recompiles and fixes the initialization error during contract queries to ensure the issue is fully resolved. It updates the system to Ledger version 1011 and Chain version 1.1.2.

**Release Info**

#### Changes:

1. Fixed initialization error during contract query (recompile)

#### Update info

* Ledger version : 1011
* Chain version : 1.1.2

***

### Release 11: Simplifies the /getAccount API by removing metadata from its response

**Completion Date:** 3 March 2026\
**Status:** Completed

**Description:** This release simplifies the /getAccount API by removing metadata from its response. It updates the system to Ledger version 1012 and Chain version 1.1.3.

**Release Info**

#### Changes:

1. Remove metadata from /getAccount api

#### Update info

* Ledger version : 1012
* Chain version : 1.1.3

***

### Release 12: PQC upgrade on testnet

**Completion Date:** 23 April 2026\
**Status:** Completed

**Description:** This release enhances smart contract capabilities by adding safe operations for `int64` and `int256` types, introducing bitwise operation support, and integrating Dilithium3 for post-quantum cryptography. It updates the system to Ledger version 1013 and Chain version 1.1.4.

**Release Info**

#### Changes:

1. Add in64/int256 safe op support in contract
2. Add bitwise op support in contract
3. Add Dilithium3 support for PQC

#### Update info

* Ledger version : 1013
* Chain version : 1.1.4

***

> This document is the official Zetrix release note.
