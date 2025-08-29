# API References

### Methods of the Chain Object

This section describes some methods of the Chain object, including:&#x20;

Chain.load, Chain.store, Chain.del, Chain.getBlockHash, Chain.tlog, Chain.getAccountMetadata, Chain.getBalance, Chain.getAccountAsset, Chain.getContractProperty, Chain.payCoin, Chain.issueAsset, Chain.payAsset, Chain.delegateCall, Chain.delegateQuery, Chain.contractCall, Chain.contractQuery, and Chain.contractCreate.

#### Chain.load

*   Description

    Get the metadata information of the contract account.
* Function call

```javascript
Chain.load(metadata_key);
```

* Parameter description
  * Metadata\_key: The keyword for metadata.
*   Example

    ```javascript
    let value = Chain.load('abc');
    /*
      
      Permission: Read-only.
      Return value: Return a string if it succeeds, such as 'values'; return false if it fails.
    */
    ```

#### Chain.store

*   Description

    Store the metadata information of the contract account.
*   Function call

    ```javascript
    Chain.store(metadata_key, metadata_value);
    ```
* Parameter description
  * metadata\_key: The keyword for metadata.
  * metadata\_key: The content of metadata.
*   Example

    ```javascript
    Chain.store('abc', 'values');
    /*
      Permission: Write
      Return value: Return true if it succeeds, or throw an exception if it fails
    */
    ```

#### Chain.del

*   Description

    Delete the metadata information of the contract account.
*   Function call

    ```javascript
    Chain.del(metadata_key);
    ```
* Parameter description
  * metadata\_key: The keyword for metadata.
  * metadata\_key: The metadata content.
*   Example

    ```javascript
    Chain.del('abc');
    /*
      Permission: Write
      Return: Return true if it succeeds, or throw an exception if it fails
    */
    ```

#### Chain.getBlockHash

*   Description

    Get block information.
*   Function call

    ```javascript
    Chain.getBlockHash(offset_seq);
    ```
* Parameter description
  * offset\_seq: The offset from the last block ranges: \[0,1024).
*   Example

    ```javascript
    let ledger = Chain.getBlockHash(4);
    /*
      Permission: Read-only
      Return value: Return a string if it succeeds, such as'c2f6892eb934d56076a49f8b01aeb3f635df3d51aaed04ca521da3494451afb3', or return false if it fails
    */
    ```

#### Chain.tlog

*   Description

    Output transaction logs.
*   Function call

    ```javascript
    Chain.tlog(topic,args...);
    ```
* Parameter description
  * tlog will generate a transaction written on the block.
  * topic: The log subject, which must be a string type with a parameter length of (0,128].
  * args...: It can contain up to 5 parameters, which can be string, numeric or Boolean type, with each parameter length (0,1024].
*   Example

    ```javascript
    Chain.tlog('transfer',sender +' transfer 1000',true);
    /*
      Permission: Write
      Return value: Return true if it succeeds, or throw an exception if it fails
    */
    ```

#### Chain.getAccountMetadata

*   Description

    Get the metadata of the specified account.
*   Function call

    ```javascript
    Chain.getAccountMetadata(account_address, metadata_key);
    ```
* Parameter description
  * account\_address: The account address.
  * metadata\_key: The keyword for metadata.
*   Example

    ```javascript
    let value = Chain.getAccountMetadata('ztxSnBFboATCEgbiDRYS7gfbe1XRsTPWJhCFE', 'abc');

    /*
      Permission: Read-only
      Return value: Return a string if it succeeds, such as 'values', or return false if it fails.
    */
    ```

#### Chain.getBalance

*   Description

    Get the coin amount of the account.
*   Function call

    ```javascript
    Chain.getBalance(address);
    ```
* Parameter description
  * address: The account address
*   Example

    ```javascript
    let balance = Chain.getBalance('ztxSnBFboATCEgbiDRYS7gfbe1XRsTPWJhCFE');
    /*
      Permission: Read-only
      Return value: Number in string format '9999111100000'
    */
    ```

#### Chain.getAccountAsset

*   Description

    Get asset information for an account
*   Function call

    ```javascript
    Chain.getAccountAsset(account_address, asset_key);
    ```
* Parameter description
  * account\_address: The account address.
  * asset\_key: The asset attributes.
*   Example

    ```javascript
    let asset_key =
    {
      'issuer' : 'ztxSnBFboATCEgbiDRYS7gfbe1XRsTPWJhCFE',
      'code' : 'CNY'
    };
    let bar = Chain.getAccountAsset('ztxSnBFboATCEgbiDRYS7gfbe1XRsTPWJhCFE', asset_key);

    /*
      Permission: Read-only
      Return value: Return the asset value such as '10000' if it succeeds, or return false if it fails
    */
    ```

#### Chain.getAccountPrivilege

*   Description

    Get privilege info for an account.
*   Function call

    ```javascript
    Chain.getAccountPrivilege(account_address);
    ```
* Parameter description
  * account\_address: The account address。
*   Example

    ```javascript
    let bar = Chain.getAccountPrivilege('ztxSnBFboATCEgbiDRYS7gfbe1XRsTPWJhCFE');

    /*
      Permission: Read-only
      Return value: If it succeeds, the permission JSON string is returned, such as' {"master" weight ": 1," thresholds ": '{" TX "threshold": 1}}'. If it fails, the false is returned.
    */
    ```

#### Chain.getContractProperty

*   Description

    Get the attributes of the contract account.
*   Function call

    ```javascript
    Chain.getContractProperty(contract_address);
    ```
* Parameter description
  * contract\_address: The contract address.
*   Example

    ```javascript
    let value = Chain.getContractProperty('ztxSgCwYLWoCZnP6s2WXtQCwhxuFxhvsr375z');

    /*
      Permission: Read-only
      Return value: Return a JSON object such as {"type":0, "length" : 416} if it succeeds, where the type refers to the contract type and the length refers to the code length of the contract, and if the account is not a contract, then the length is 0; return false if it fails.
      
    */
    ```

#### Chain.payCoin

*   Description

    Transfer gas.
*   Function call

    ```javascript
    Chain.payCoin(address, amount[, input]);
    ```
* Parameter description
  * address: The target address.
  * amount: The amount of Gas.
  * input: Optional, the contract parameter. By default, it is an empty string if it is not filled in.
*   Example

    ```javascript
    Chain.payCoin("ztxSnBFboATCEgbiDRYS7gfbe1XRsTPWJhCFE", "10000", "{}");
    /*
      Permission: Write
      Return value: Return true if it succeeds, or throw an exception if it fails  
    */
    ```

#### Chain.issueAsset

*   Description

    Issue assets.
*   Function call

    ```javascript
    Chain.issueAsset(code, amount);
    ```
* Parameter description
  * code: The asset code.
  * amount: The amount of the asset to be issued.
*   Example

    ```javascript
    Chain.issueAsset("CNY", "10000");
    /*
      Permission: Write
      Return value: Return true if it succeeds, or throw an exception if it fails  
    */
    ```

#### Chain.payAsset

*   Description

    Transfer assets
*   Function call

    ```javascript
    Chain.payAsset(address, issuer, code, amount[, input]);
    ```
* Parameter description
  * address: The target address.
  * issuer: The asset issuer.
  * code: The asset code.
  * amount: The amount to be transferred.
  * input: Optional, the contract parameter. By default, it is an empty string if it is not filled in.
*   Example

    ```javascript
    Chain.payAsset("ztxSnBFboATCEgbiDRYS7gfbe1XRsTPWJhCFE", "ztxSj9kGyXR2YpyxwZVMrnGcLWoG3Hf9qXne8", "CNY", "10000", "{}");
    /*
      Permission: Write
      Return value: Return true if it succeeds, or throw an exception if it fails    
    */
    ```

#### Chain.delegateCall

*   Description

    Delegate call.
*   Function call

    ```javascript
    Chain.delegateCall(contractAddress, input);
    ```
*   Parameter description

    * contractAddress: The address of the contract to be called.
    * input：Input parameter.

    The `Chain.delegateCall` function will trigger the `main` function of the contract to be called, and the Chain.delegateCall function will assign the execution environment of the current contract to the contract to be called.
*   Example

    ```javascript
    let ret = Chain.delegateCall('ztxSgGkHuPv6FQUu6C5uWnaqyKJ22bSNrJUCJ'，'{}');
    /*
      Permission: Write
      Return value: Return a result if it succeeds, or throw an exception if it fails.
    */
    ```

#### Chain.delegateQuery

*   Description

    Delegate query.
*   Function call

    ```javascript
    Chain.delegateQuery(contractAddress, input);
    ```
*   Parameter description

    * contractAddress: The address of the contract to be called.
    * input：Input parameter.

    The `Chain.delegateQuery` function will trigger the `query` function of the contract to be called, and the Chain.delegateQuery function will assign the execution environment of the current contract to the contract to be called.
*   Example

    ```javascript
    let ret = Chain.delegateQuery('ztxSgGkHuPv6FQUu6C5uWnaqyKJ22bSNrJUCJ'，"");
    /*
      Permission: Read-only
      Return value: If the target account is a normal account, it returns true. If the target account is a contract, and the call succeeds, the string {"result":"4"} is returned, where the value of the result field is the specific result of the query, and if the call fails return {"error ":true} string.
    */
    ```

#### Chain.contractCall

*   Description

    Call contracts.
*   Function call

    ```javascript
    Chain.contractCall(contractAddress, asset, amount, input);
    ```
*   Parameter description

    * contractAddress: The address of the contract to be called
    * asset: The asset class, true for Gas, object {"issue": adxxx, "code" : USDT} for assets.
    * amount: The amount of the asset.
    * input：Input parameter.

    The `Chain.contractCall` function triggers the `main` function entry of the contract to be called.
*   Example

    ```javascript
    let ret = Chain.contractCall('ztxSgGkHuPv6FQUu6C5uWnaqyKJ22bSNrJUCJ'，true, toBaseUnit("10"), "");
    /*
      Permission: Write
      Return value: Return true if the target account is a normal account. If the target account is a contract, the return value of the main function is returned if the call succeeds, and an exception is thrown if the call fails.
    */
    ```

#### Chain.contractQuery

*   Description

    Query contracts.
*   Function call

    ```javascript
    Chain.contractQuery(contractAddress, input);
    ```
*   Parameter description

    * contractAddress: The address of the contract to be called
    * input：Input parameter.

    The `Chain.contractQuery` function will call the `query` interface of the contract.
*   Example

    ```javascript
    let ret = Chain.contractQuery('ztxSgGkHuPv6FQUu6C5uWnaqyKJ22bSNrJUCJ'，"");
    /*
      Permission: Read-only
      Return value: If the call succeeds, the string {"result":"xxx"} is returned, where the value of the result field is the specific result of the query, and if the call fails, return a string of {"error":true}.
    */
    ```

#### Chain.contractCreate

*   Description

    Create Contracts.
*   Function call

    ```javascript
    Chain.contractCreate(balance, type, code, input);
    ```
*   Parameter description

    * balance: The asset that is transferred to the contract created, in string.
    * type :0 indicates javascript, in integer.
    * code: The contract code, in string.
    * input：The initiation parameter of the init function.

    The `Chain.contractCreate` function create contracts.
*   Example

    ```javascript
    let ret = Chain.contractCreate(toBaseUnit("10"), 0, "'use strict';function init(input){return input;} function main(input){return input;} function query(input){return input;} ", "");
    /*
      Permission: Write
      Return value: Return the contract address if it is created successfully, or throw an exception if it fails.
    */
    ```

### Variables of the Chain Object

This section outlines key variables provided by the `Chain` object in Zetrix smart contracts, which offer access to contextual blockchain, transaction, and message-level data:

#### Chain.block

* The `Chain.block` object enables smart contracts to make decisions based on the current blockchain state, ensuring time-sensitive and sequence-sensitive logic executes reliably.

**Chain.block.timestamp**

*   Variable description

    The timestamp of the block when the current transaction is executed.

**Chain.block.number**

*   Variable description

    The height of the block where the current transaction is executed.

Chain.tx

*   Variable description

    The transaction information signed by the user at the time of the transaction.

**Chain.tx.initiator**

*   Variable description

    The original originator of the transaction, that is the fee payer of the transaction.

**Chain.tx.sender**

*   Variable description

    The most primitive trigger of the transaction, that is the account in the transaction that triggers the execution of the contract. For example, an account initiates a transaction, and an operation in the transaction is to call the contract Y (the source\_address of the operation is x), then the value of the sender is the address of the account x during the execution of the contract Y.
*   Example

    ```javascript
    let bar = Chain.tx.sender;
    /*  
     Then the value of bar is the account address of x.
    */
    ```

**Chain.tx.gasPrice**

*   Variable description

    The price of the gas in the transaction signature.

**Chain.tx.hash**

*   Variable description

    The hash value of the transaction.

**Chain.tx.feeLimit**

*   Variable description

    The limit fee for the transaction.

#### Chain.msg

A **message** in Zetrix smart contracts refers to the contextual data that initiates contract execution within a transaction. While the **transaction data remains immutable** during execution, the **message object (`Chain.msg`) can be updated** as the contract interacts with other contracts. For instance, when using functions like `Chain.contractCall` or `Chain.contractQuery`, the message context switches to reflect the called contract’s perspective. This allows the contract to correctly process the execution flow and maintain state awareness across inter-contract interactions.

**Chain.msg.initiator**

*   Variable description

    The original originator account for this message.

**Chain.msg.sender**

*   Variable description

    The account number for triggering this message.
*   Example

    For example, an account initiates a transaction, and an operation in the transaction is to call the contract Y (the source\_address of the operation is x), then the value of the sender is the address of the account x during the execution of the contract Y.

    ```javascript
    let bar = Chain.msg.sender;
    /*
    Then the value of bar is the account address of x.
    */
    ```

**Chain.msg.coinAmount**

*   Variable description

    The Gas for this payment operation

**Chain.msg.asset**

*   Variable description

    The assets for this payment operation
*   Example

    ```json
    {
        "amount": 1000, 
        "key" : {
            "issuer": "ztxSnBFboATCEgbiDRYS7gfbe1XRsTPWJhCFE", 
            "code":"CNY"
        }
    }
    ```

**Chain.msg.nonce**

*   Variable description

    The nonce value of the initiator in this transaction, ie the nonce value of the Chain.msg.initiator account.

**Chain.msg.operationIndex**

*   Variable description

    The sequence number for triggering this contract calling operation.
*   Example

    For example, an account A initiates a transaction tx0, and tx0 has a 0th (counting from 0) operation which is to transfer assets to a contract account (contract call), then the value of `Chain.msg.operationIndex` is 0.

    ```javascript
    let bar = Chain.msg.operationIndex;
    /* bar is a non-negative integer*/
    ```

#### Chain.thisAddress

*   Variable description

    The address of the current contract account.
*   Example

    For example, the account x initiates a transaction to call contract Y. During this execution, the value is the address of the contract account Y.

    ```js
    let bar = Chain.msg.thisAddress;
    /*
     The value of bar is the account address of the contract Y.
    */
    ```

### Methods of the Utils Object

The `Utils` object in Zetrix smart contracts provides a comprehensive set of utility functions that assist in logging, mathematical operations, cryptographic verification, and data validation. These methods are essential for ensuring accurate, secure, and efficient smart contract execution. The `Utils` object includes the following methods:

Utils.log, Utils.stoI64Check, Utils.int64Add, Utils.int64Sub , Utils.int64Mul, Utils.int64Mod, Utils.int64Div, Utils.int64Compare, Utils.assert, Utils.sha256, Utils.ecVerify, Utils.toBaseUnit, Utils.addressCheck and Utils.toAddress.

#### Utils.log

* Description
  * `Utils.log(message)`: Outputs logs for debugging purposes during contract execution.
*   Function call

    ```javascript
    Utils.log(info);
    ```
* Parameter description
  * info: The log content.
*   Example

    ```javascript
    let ret = Utils.log('hello');
    /*
      Permission: Read-only
      Return value: If it succeeds, no value will be returned, and a snippet of Trace log will be output in the process of executing the contract, such as V8contract log[adxSnBFboATCEgbiDRYS7gfbe1XRsTPWJhCFE:hello], or return false if it fails.
    */
    ```

#### Utils.stoI256Check

*   Description

    Legal check for string numerics.
*   Function call

    ```javascript
    Utils.stoI256Check(strNumber);
    ```
* Parameter description
  * strNumber: String numeric parameter
*   Example

    ```javascript
    let ret = Utils.stoI256Check('12345678912345');
    /*
      Permission: Read-only
      Return value: Return true if it succeeds, or return false if it fails.
    */
    ```

#### Utils.int256Add

*   Description

    256-bit addition.
*   Function call

    ```javascript
    Utils.int256Add(left_value, right_value);
    ```
* Parameter description
  * left\_value: Left value.
  * right\_value: Right value.
*   Example

    ```javascript
    let ret = Utils.int256Add('12345678912345', 1);
    /*
      Permission: Read-only
      Return value: Return a string if it succeeds, such as '12345678912346', or throw an exception if it fails.
    */
    ```

#### Utils.int256Sub

*   Description

    256-bit subtraction.
*   Function call

    ```javascript
    Utils.int256Sub(left_value, right_value);
    ```
* Parameter description
  * left\_value: Left value.
  * right\_value：Right value.
*   Example

    ```javascript
    let ret = Utils.int256Sub('12345678912345', 1);
    /*
      Permission: Read-only
      Return value: Return a string such as '12345678912344' if it succeeds, or throw an exception if it fails.
    */
    ```

#### Utils.int256Mul

*   Description

    256-bit multiplication.
*   Function call

    ```javascript
    Utils.int256Mul(left_value, right_value);
    ```
* Parameter description
  * left\_value: Left value.
  * right\_value：Right value.
*   Example

    ```javascript
    let ret = Utils.int256Mul('12345678912345', 2);
    /*
      Permission: Read-only
      Return value: Return a string such as '24691357824690' if it succeeds, or throw an exception if it fails.
    */
    ```

#### Utils.int256Mod

*   Description

    256-bit modulo.
*   Function call

    ```javascript
    Utils.int256Mod(left_value, right_value);
    ```
* Parameter description
  * left\_value: Left value.
  * right\_value: Right value.
*   Example

    ```javascript
    let ret = Utils.int256Mod('12345678912345', 2);
    /*
      Permission: Read-only
      Return value: Return a string such as '1' if it succeeds, or throw an exception if it fails.
    */
    ```

#### Utils.int256Div

*   Description

    256-bit division.
*   Function call

    ```javascript
    Utils.int256Div(left_value, right_value);
    ```
* Parameter description
  * left\_value: Left value.
  * right\_value: Right value.
*   Example

    ```javascript
    let ret = Utils.int256Div('12345678912345', 2);
    /*
      Permission: Read-only
      Return value: Return '6172839456172' if it succeeds, or throw an exception if it fails.
    */
    ```

#### Utils.int256Compare

*   Description

    256-bit comparison.
*   Function call

    ```javascript
    Utils.int256Compare(left_value, right_value);
    ```
* Parameter description
  * left\_value: Left value.
  * right\_value: Right value.
*   Example

    ```javascript
    let ret = Utils.int256Compare('12345678912345', 2);
    /*
      Permission: Read-only
      Return value: Return 1 if it succeeds (the left value is greater than the right value), or throw an exception if it fails.
    */
    ```
*   Return value

    1: left value is greater than right value, 0: left value equals to right value, -1: left value less than right value.

#### Utils.assert

*   Description

    256 assertion.
*   Function call

    ```javascript
    Utils.assert(condition[, message]);
    ```
* Parameter description
  * condition: Assertive variable
  * message: Optional, an exception message is thrown when it fails
*   Example

    ```javascript
    Utils.assert(1===1, "Not valid");
    /*
      Permission: Read-only
      Return value: Return true if it succeeds, or throw an exception if it fails  
    */
    ```

#### Utils.sha256

*   Description

    sha256 computation.
*   Function call

    ```javascript
    Utils.sha256(data[, dataType]);
    ```
* Parameter description
  * data: The raw data of the hash to be calculated. According to the dataType, fill in the data in different formats.
  * dataType: The data type, integer, optional field, by default is 0. 0: base16 encoded string, such as "61626364"; 1: ordinary original string, such as "abcd"; 2: base64 encoded string, such as "YWJjZA==". If you are calculating binary data, it is recommended to use base16 or base64 encoding.
*   Return value

    Return a base16 encoded string if it succeeds, or return false if it fails.
*   Example

    ```javascript
    let ret = Utils.sha256('61626364');
    /*
      Permission: Read-only
      Function: Right
      Return value: Return a 64-byte base16 string if it succeeds, such as '88d4266fd4e6338d13b845fcf289579d209c897823b9217da3e161936f031589', or return false it fails.
    */
    ```

#### Utils.ecVerify

*   Description

    Check if the signature is legal.
*   Function call

    ```javascript
    Utils.ecVerify(signedData, publicKey,blobData [, blobDataType]);
    ```
* Parameter description
  * signedData: The signature data, a string encoded by base16.
  * publicKey: The public key, a string encoded by base16.
  * blobData: The raw data, fill in different formats of data per blobDataType.
  * blobDataType: The blobData type, integer, optional field, the default is 0. 0: base16 encoded string, such as "61626364"; 1: ordinary original string, such as "abcd"; 2: base64 encoded string, such as "YWJjZA==". If you are verifying binary data, it is recommended to use base16 or base64 encoding.
*   Return value

    Return true if it succeeds, or return false if it fails.
*   Example

    ```javascript
    let ret = Utils.ecVerify('3471aceac411975bb83a22d7a0f0499b4bfcb504e937d29bb11ea263b5f657badb40714850a1209a0940d1ccbcfc095c4b2d38a7160a824a6f9ba11f743ad80a', 'b0014e28b305b56ae3062b2cee32ea5b9f3eccd6d738262c656b56af14a3823b76c2a4adda3c', 'abcd', 1);
    /*
      Permission: Read-only
      Return value: Return true if it succeeds, or return false if it fails
    */
    ```

#### Utils.toBaseUnit

* Description

Transform the unit.

*   Function call

    ```javascript
    Utils.toBaseUnit(value);
    ```
* Parameter description
  * value: The converted number, only string is allowed to pass in, and it can contain a decimal point, which allows up to 6 digits after the decimal point.
*   Return value

    Return a string multiplied by 10^6 if it succeeds or return false if it fails.
*   Example

    ```javascript
    let ret = Utils.toBaseUnit('12345678912');
    /*
      Permission: Read-only
      Return value: Return a string '1234567891200000000' if it succeeds, or throw an exception if it fails.
    */
    ```

#### Utils.addressCheck

*   Description

    Address legality check.
*   Function call

    ```javascript
    Utils.addressCheck(address);
    ```
* Parameter description
  * address The address parameter in string.
*   Return value

    Return true if it succeeds, or return false if it fails.
*   Example

    ```javascript
    let ret = Utils.addressCheck('ztxSj9kGyXR2YpyxwZVMrnGcLWoG3Hf9qXne8');
    /*
      Permission: Read-only
      Return value: Return true if it succeeds, or return false if it fails.
    */
    ```

#### Utils.toAddress

*   Description

    Transform a public key to an address.
*   Function call

    ```javascript
    Utils.toAddress(public_key);
    ```
* Parameter description
  * public\_key The public key, a base16 encoded string
*   Return value

    Return the account address if it succeeds, or return false if it fails.
*   Example

    ```javascript
    let ret = Utils.toAddress('b0016ebe6191f2eb73a4f62880b2874cae1191183f50e1b18b23fcf40b75b7cd5745d671d1c8');
    /*
      Permission: Read-only
      Return value: Return "adxSkMvC2RcvXLpDgXBJCSb4m9mPe8gpNDKAG" if it succeeds, or return false if it fails.
    */
    ```

#### Utils.bcRangeProofVerify

*   Description

    Verifying the certificate for scope (for privacy protection).
*   Function call

    ```javascript
    Utils.bcRangeProofVerify(commit, proof);
    ```
* Parameter description
  * commit, 66 bit Pedersen commitment string
  * proof, Range proof string, variable length
*   Return value

    Return true if it succeeds, or return false if it fails.
*   Example

    ```javascript
    let ret = Utils.bpRangeProofVerify('097ff10254037114cae985f044189a3faa044ac32c26ffadc0f08dc4b81a536638', '410905f651c9448e8a4e6db10c0bc23b2b09cf37ee2b6909b0e9178cf89b371584ea8533ed9648a942feb6b797bbdd8e9cd49ab8a86e6bf89f7e3b2c35c01818ad9006329e8dc4c9e335d60021b13c33c8aa8bd471908be4ce74a4cdfe968006abc3819e9e7d0aba261b947ed9bd8706d439707e783e34323c7c24b7c3dd722b66546f5c0d7c66d6c5a7390cc4cd1ed4ef53a0b1db3778a56b0ff418bfcababd966ae82cf0f44c29ba27f9eaa0a9ca1b4c913ddb7e42453666ccfbaad55f981b8cbede5023705cfeb08a26b323ea05b06ba3853cbfe02315c335f742c6a4ca290ca3d31a619534a38fc0754a6b52b9b66e69088a6265a0e68914b1a230bbd6d742875aac47259be01d60fff5c301c552a66b81152b2b2d7dca9064cde271690e30a7cae5fee6b89e0bd7a5c571d561f65a7d34cb4f5aba49fbeef91132e229fdf38ca61c2c737d3055ea143d0e5732b43ade6d7cf66269c7b3333f1ee9a295fc7f5d3cf47770d9a15651d176fa2792e39ad4bdacb14eb13657faf5eed43f3620a09441a5032c4919744692844a214455204662e9f7f80c88a569ab1a1986186f35a0eefd53d7be56d1150ed6a0cb030a10de35d0cb25738d66234646998b70dc6210ec8985c9bda183dd0a0d985c1bf1255afc326fd86029bc88e2db0433061a0bf6be107d6ddebb2516843b9c6a56f9e03a420e4665bc013f7e6a497412015d4fed2cf9895c756857d5434799e20fbf8feef4473c778bd06fc997ea9772bec1d0cc30870580575017239cef0f693922c12a1ab850628302f970d07caaa54196c5ed59eefaaad981dfeeef4efe06d5ab3634b8e9882416cde960dee25f22701faf63086771d2987e3b300659278cc9e8543ee81c9f70ebbe2897f1020c609b3fe1667358b5998dbf089720cecc401810acbb48864262065c7742c5cd2b83b76aafc6ff2428e51a66e89ca1b1cf552bb8f8a261fb2e91e49ea68fc2db1c00a079bf2b0b39c9f43be25d31136399666ff05dcbbbc98d47358a5052c0d3cd113f9f0a3fe6838babd160411f22d6a187f73b69e45f042149e7e1e879a8b75c6ac4dfa4bbb473f22f89738f163f2a64efb20a0bb67e16acd06791080cc5246d48887a158f8b');
    /*
      Permission: Read-only
      Return value: Return true if it succeeds, or return false if it fails.
    */
    ```

#### Utils.pedersenTallyVerify

*   Description

    Verifying the equal for Output and input (for privacy protection).
*   Function call

    ```javascript
    Utils.pedersenTallyVerify(input_commits, output_commits, excess_msg, excess_sig);
    ```
* Parameter description
  * input\_commits, Enter Pedersen commitment list, JSON array
  * output\_commits, Output Pedersen commitment list, JSON array
  * excess\_msg, Message to be signed, 32-bit string, no encoding requirements
  * excess\_sig, Signature result string of transaction core (input minus output remainder)
*   Return value

    Return true if it succeeds, or return false if it fails.
*   Example

    ```javascript
    let ret = Utils.pedersenTallyVerify(['08aab3b3c403129887d1f5932565c0fd9ef7b8d562de6eb0b6eb64c1b7396d268a'], ['08a64f2ed7be3940dfb5b76b450418524df48837595c98698dbe191708cb911c40', '09c28bb504d0c4e4ea4f19e3041a90b608aada114fab1786175ffaefd5fe6c0d3b'], 'b649bd965a5126984cc785cbeb1ef053', '304402202345c4e1f4efd8bfdb77681e7d638f9a8f29f473e47a698c56eeab3fe3bc278502206bbdf0db0e04bafa4a88c0382b9b600e72976fceeaa9129cb257a82a89ee52fb');
    /*
      Permission: Read-only
      Return value: Return true if it succeeds, or return false if it fails.
    */
    ```

### **Exception Handling in Zetrix Smart Contracts**

Zetrix smart contracts use JavaScript-style exception handling. When an exception occurs and is not caught during execution, it triggers specific failure behavior defined by the blockchain environment.

**JavaScript Exceptions**

* If an **uncaught exception** occurs during contract execution:
  * The **entire contract execution fails**.
  * **All state changes and transactions** initiated by the contract are **reverted**—nothing takes effect.
  * The transaction that triggered the contract is marked as **failed**, and an error code **151** is returned to indicate the failure.

**Transactional Integrity**

* A contract may initiate **multiple internal transactions** (e.g., transfers, state updates).
* If **any single transaction** within the contract fails, it throws an exception.
* This causes the **entire contract execution to abort**, and all previous operations in that call are **rolled back**, ensuring atomicity.

**Implication**

* Developers must use `try-catch` logic and proper assertions (`Utils.assert`) to validate conditions and handle potential failures gracefully.
* Failing to do so will result in complete transaction failure, impacting contract reliability and user experience.

This strict exception handling ensures **consistency, security, and predictability** in the Zetrix smart contract environment.
