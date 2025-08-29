# Smart Contract

## Introduction to Zetrix Smart Contract

### Overview

Zetrix smart contracts are written in standard JavaScript (ECMAScript as defined in ECMA-262) and follow a structured format. Each contract includes an `init` function for initialization, a `main` function as the primary execution entry point, and a `query` function for retrieving data. These functions accept parameters as stringified input when invoked, enabling controlled and predictable contract behavior within the Zetrix execution environment.

The following is a simple example:

```javascript
"use strict";
function init(input)
{
  /*init whatever you want*/
  return;
}

function main(input)
{
  let para = JSON.parse(input);
  if (para.do_foo)
  {
    let x = {
      'hello' : 'world'
    };
  }
}

function query(input)
{ 
  return input;
}
```

### Objects in the Interfaces

In Zetrix smart contracts, the global objects `Chain` and `Utils` are provided to facilitate blockchain interaction. These objects offer a range of methods and variables that allow contracts to access blockchain state data and initiate transactions. While they support operations such as querying balances, issuing assets, and logging transactions, they do not handle account-level configurations like setting thresholds or weights.

> **Note:** Custom variables in Zetrix smart contracts must not use the same names as built-in objects (e.g., `Chain`, `Utils`). Doing so can lead to unpredictable behavior and data conflicts, potentially resulting in execution errors or corrupted contract logic. Always use unique and clearly defined variable names to maintain contract integrity.

#### Method Usage

Object. method (variable)

*   Obtain the account balance

    ```javascript
    Chain.getBalance('ztxSnBFboATCEgbiDRYS7gfbe1XRsTPWJhCFE');
    ```
*   Print logs

    ```javascript
    Utils.log('hello');
    ```
*   The current block number

    ```javascript
    Chain.block.number;
    ```

#### Read and Write Privileges

1. Each function within the `Chain` and `Utils` objects in Zetrix smart contracts is assigned a fixed permission level—either **read-only** or **write**:
   * **Read-Only Functions**: These do not modify blockchain state. Examples include `Chain.getBalance`, which retrieves an account’s balance.
   * **Write Functions**: These modify blockchain data. For instance, `Chain.payCoin` performs token transfers and writes to the ledger.
2. When developing smart contracts, it's important to observe function call permissions based on the contract entry point:
   * The `init` and `main` functions can invoke both read-only and write operations.
   * The `query` function is restricted to read-only calls. Attempting to execute a write operation within `query` will result in an "undefined" error during execution or debugging.

#### Return Value

When invoking an internal function in a Zetrix smart contract, the function should return `false` upon failure or throw an exception to terminate execution. Successful execution continues normally for other objects. If a parameter error occurs, the error message will specify the index of the faulty parameter starting from 0. For example, if the message indicates “parameter 1,” it means the second parameter in the function call is incorrect. This helps developers quickly identify and correct input issues during contract development or debugging.

The following is an example:

```javascript
Chain.issueAsset("CNY", 10000);
/*
    Error description:
    Contract execute error,issueAsset parameter 1 should be a string

    It means that the second parameter should be a string
*/
```

The next section will explain in detail the APIs available when writing smart contract on Zetrix.
