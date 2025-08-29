---
description: >-
  Node.js developers can seamlessly interact with the Zetrix blockchain using
  the Zetrix Node.js SDK, enabling efficient integration and operation.
---

# Node.js

## Overview

The **Zetrix Node.js SDK** enables developers to easily work with the Zetrix blockchain by offering a streamlined interface for complex operations. This powerful toolkit simplifies tasks such as **account management**, **smart contract deployment**, and **transaction creation**, allowing developers to focus on building advanced applications with ease and efficiency.

## Target Audience

The **Zetrix Node.js SDK** is tailored for developers proficient in **JavaScript** and **Node.js**, aiming to build API services that interact with the Zetrix blockchain. Whether you're an experienced blockchain developer or just starting out, the SDK provides an accessible and developer-friendly entry point to building on Zetrix.

## Quick Start Guide

### Installation

Getting started with the Zetrix Node.js SDK is straightforward. Follow these steps to install the package:

```
$ npm install zetrix-sdk-nodejs
```

We also recommend using the [dotenv](https://www.npmjs.com/package/dotenv) package to conveniently manage environment variables.

```
$ npm install dotenv
```

### Prerequisites

* [Zetrix Wallet](https://www.zetrix.com/zetrix-wallet/) installed.
* A Zetrix account with sufficient balance. Claim your faucet [here](https://faucet.zetrix.com/).

### Configuration

Create a **.env** file in the root of your project and insert your key/value pairs in the following format of KEY=VALUE:

For Zetrix Testnet:

```
NODE_URL=test-node.zetrix.com
```

For Zetrix Mainnet:

```
NODE_URL=node.zetrix.com
```

## Basic Usages

Included in this section are some sample code snippets to help you get started with the SDK.

Create the `zetrix-sdk-nodejs` instance to begin using the SDK:

```javascript
'use strict';

const ZtxChainSDK = require('zetrix-sdk-nodejs');

const sdk = new ZtxChainSDK({
  host: process.env.NODE_URL,
});
```

Retrieving account balance using the SDK:

```javascript
// Retrieve account balance by passing the address
sdk.account.getBalance(address).then(resp => {
  if (resp.errorCode === 0) {
    console.log(resp.result.balance);
  }
}).catch(err => {
  console.log(err.message);
});
```

Creating a new account using the SDK:

```javascript
// Create a new account onchain
sdk.account.create().then(data => {
  console.log(data);
}).catch(err => {
  console.log(err.message);
});
```

Sample contract call using the SDK:

```javascript
// Querying a contract
const data = yield sdk.contract.call({
    optType: 2,
    // Insert contract address
    contractAddress: contractAddress, 
    // Pass input parameters as a JSON string
    input: JSON.stringify({
      // Calling the 'getCertificateBySerialNumber' query function from the smart contract
      method: 'getCertificateBySerialNumber',
      // Passing the paramets for querying
      params: {
        serialNumber: "1237"
      }
    }),
  });
```

Another [sample](https://github.com/Zetrix-Chain/zetrix-sdk-nodejs/blob/main/example/exchange.js#L621) contract invocation:

```javascript
// Invoke a contract by sending tokens
const operationInfo = await sdk.operation.contractInvokeByGasOperation({
  sourceAddress: newAddress,
  contractAddress: contractAddress,
  // 0 ZETA will be sent
  amount: 0,
  // Input destination address under "to" and number of tokens under "value"
  input: '{\"method\":\"transfer\",\"params\":{\"to\":\"ZTX3Ta7d4GyAXD41H2kFCTd2eXhDesM83rvC3\",\"value\":\"10000000\"}}',
  metadata: 'invoking contract by sending tokens. 0 ZETRIX (gas) amount is sent'
});
```

More examples can be found in the [examples](https://github.com/Zetrix-Chain/zetrix-sdk-nodejs/tree/main/example) and [test](https://github.com/Zetrix-Chain/zetrix-sdk-nodejs/tree/main/test) folder in the `zetrix-sdk-nodejs` repository.

## API References

### Account APIs

#### Creating onchain account

```
sdk.account.create()
```

Allows the user to create a new account on Zetrix. A keypair will be generated consisting of the address and the private key to the newly-created account.

#### Retrieving account information

```
sdk.account.getBalance(address)
```

Allows the user to retrieve the account balance of the given address.

Parameter:

* `address` : a string for a valid account address on Zetrix

```
sdk.account.checkValid(address)
```

Allows user to check if the account address is valid or invalid.

Parameter

* `address` : a string for an account address to be checked

```
sdk.account.getNonce(address)
```

Allows the user to get the nonce for the particular account address.

Parameter

* `address` : a string for a valid account address on Zetrix

```
sdk.account.getInfo(address)
```

Allows the user to retrieve the address, balance, nonce, other related assets, voting weightage and transaction threshold.

Parameter

* `address` : a string for a valid account address on Zetrix

```
sdk.account.getAssets(address)
```

Allows the user to retrieve the assets in the given address.

Parameter

* `address` : a string for a valid account address on Zetrix

```
sdk.account.getMetadata(object)
```

Allows the user to retrieve metadata associated with the given account address.

Parameter - JSON object with the following fields:

* `address` : a string for a valid account address on Zetrix
* `key` : The private key used to access the above address &#x20;

```
sdk.account.isActivated(address)
```

Allows the user to check if the address is activated onchain.

Parameter

* `address` : a string for an account address to be checked



#### Account operation

```
sdk.operation.accountSetMetadataOperation(object)
```

Allows the user to attach/associate metadata to a particular account address.

Parameter - JSON object with the metadata (in the form of key-value pairs) you want to associate to a particular account address

```
sdk.operation.accountSetPrivilegeOperation(object)
```

Allows the user to set the privilege such as the voting weightage and transaction threshold count.

Parameter - JSON object with the following fields:

* `signers` : array containing objects that key-value pairs of "address" (Zetrix account address) and "weight" (weightage of the signer out of 100)
* `typeThresholds` : array containing objects that key-value pairs of "type" and "threshold" (threshold weightage)



### Transaction APIs

#### Creating and submitting transactions

```
sdk.transaction.buildBlob(object)
```

Allows the user to build a transaction blob before submitting a transaction. The return object will contain the transaction blob required to sign the transaction.

Parameter - JSON object with the following fields:

* `sourceAddress` : account address of sender in string
* `gasPrice` : the number of ZETA to be paid per unit of operation
* `feeLimit` : the limit to how much fees will be spent for a transaction (in ZETA)
* `nonce` : the nonce associated with the account address
* `operations` : a list containing an object with `type` (specified as a string) of the operation and the associated `data` (in an object containing key-value pairs of metadata)

```
sdk.transaction.evaluateFee(object)
```

Allows the user to evaluate the fee for making a transaction given an operation.

Parameter

* `sourceAddress` : account address of the transaction initiator in string
* `nonce` : the nonce associated with the account address
* `operations` : a list containing an object with `type` (specified as a string) of the operation and the associated `data` (in an object containing key-value pairs of metadata)
* `signtureNumber` : a random string of numbers

```
sdk.transaction.sign(object)
```

Allows the user to sign the transaction before submitting. The return object will contain the signature needed to submit the transaction to the blockchain.

Parameter - JSON object with the following fields:

* `privateKeys` : a string containing the private key to the account making the transaction stored within a list
* `blob` : Blob generated from the buildBlob function above

```
sdk.transaction.submit(object)
```

Allows the user to submit the transaction to the blockchain.

Parameter - JSON object with the following fields:

* `signature` : a string containing the private key to the account making the transaction stored within a list
* `blob` : Blob generated from the buildBlob function above



#### Retrieving transaction information

```
sdk.transaction.getInfo(txHash)
```

Allows the user to get information related to the transaction.

Parameter

* `txHash` : a string containing the hash of the transaction



**Sending ZETRIX & other assets**

```
sdk.operation.gasSendOperation(object)
```

Allows the user to send ZETRIX to a destination address.

Parameter - JSON object with the following fields:

* `sourceAddress` : a string of the sender's account address
* `destAddress` : a string of the receiver's account address
* `gasAmount` : a string of the amount of ZETRIX to be sent, specified in ZETA
* `metadata` : a string as a memo for the transaction



### Contract APIs

#### Invoking smart contract

```
sdk.contract.call(object)
```

Allows the user to invoke a contract.

Parameter - JSON object with the following fields:

* `contractAddress` : a string of the address of the contract to be invoked
* `gasPrice` : the number of ZETA to be paid per unit of operation
* `feeLimit` : the limit to how much fees will be spent for a transaction (in ZETA)
* `input` : a JSON string containing the method to be called and the necessary parameters
* `optType` : the operation type number

#### Retrieving smart contract information

```
sdk.contract.getAddress(txHash)
```

Allows the user to obtain the contract address from a given transaction hash.

Parameter

* `txHash` : a string of the transaction hash

```
sdk.contract.getInfo(contractAddress)
```

Allows the user to obtain contract information like the contract type and the payload for a given contract address.

Parameter

* `contractAddress` : A string of the contract address

```
sdk.contract.checkValid(contractAddress)
```

Allows the user to verify if the contract is valid.

Parameter

* `contractAddress` : A string of the contract address



#### Smart contract operations

```
sdk.operation.contractCreateOperation(object)
```

Allows the user to create a contract before it is deployed.

Parameter - JSON object with the following fields:

* `sourceAddress` : A string of the address of the account creating the contract operation
* `initBalance` : Initialize Zetrix balance for smart contract
* `type` : The operation type number (refer to this)
* `payload` : The content of the smart contract to be created
* `initInput` : Parameters passed to initialize smart contract through the `init`

```
sdk.operation.contractInvokeByGasOperation(object)
```

Allows the user to send tokens.

Parameter - JSON object with the following fields:

* `contractAddress` : A string of the contract address of the token
* `sourceAddress` : A string of the address invoking the contract
* `amount` : The amount of Zetrix to be sent (defined in ZETA)
* `input` : JSON string specifying the method and parameters (destination address and amount to transfer)
* `metadata` : A string as a memo for transferring the tokens

```
sdk.operation.contractUpgradeOperation(object)
```

Allows the user to upgrade contract and the owner of the contract.

Parameter - JSON object with the following fields:

* `contractAddress` : A string of the contract address to be upgraded
* `sourceAddress` : A string of the address initiating the contract upgrade
* `sPayload` : A boolean flag indicating whether there is a new payload which contains the upgraded smart contract
* `payload` : A string of the new upgraded contract code
* `sOwner` : A boolean flag indicating whether there is a new owner for the contract
* `owner` : A string of the new contract owner
* `metadata` : Optional additional information about the upgrade



### Block APIs

#### Getting block information

```
sdk.block.getNumber()
```

Allows the user to retrieve the block height.

```
sdk.block.checkStatus()
```

Allows the user to check the latest block status.

```
sdk.block.getTransactions(blockNum)
```

Allows the user to retrieve the transactions included in the specified block.

Parameter

* `blockNum` : A string of the block number

```
sdk.block.getInfo(blockNum)
```

Allows the user to retrieve information related to the block such as the closing time of the block and the number of transactions included in the block.

Parameter

* `blockNum` : A string of the block number

```
sdk.block.getLatestInfo()
```

Allows the user to retrieve information related to the latest block such as the closing time of the block and the number of transactions included in the block.

```
sdk.block.getValidators(blockNum)
```

Allows the user to retrieve the list of validators for the given block number.

Parameter

* `blockNum` : A string of the block number

```
sdk.block.getLatestValidators()
```

Allows the user to retrieve the list of validators.

```
sdk.block.getReward(blockNum)
```

Allows the user to get the reward distributed for the given block.

Parameter

* `blockNum` : A string of the block number

```
sdk.block.getLatestReward()
```

Allows the user to get the latest reward distribution info.

```
sdk.block.getFees(blockNum)
```

Allows the user to retrieve the fees paid for creating a given block.

Parameter

* `blockNum` : A string of the block number

```
sdk.block.getLatestFees()
```

Allows the user to retrieve the latest block creation fee.



### Blockchain Interactions

```
sdk.operation.logCreateOperation(object)
```

Allows the user to create a log on the blockchain.

Parameter

* `sourceAddress` : address of the logger
* `topic` : topic of the log in string
* `data` : data to include in the log in string

### For more information on how you could access Zetrix's Node.JS SDK, please visit our [GitHub repository](https://github.com/Zetrix-Chain/zetrix-sdk-nodejs).
