---
description: >-
  Go developers can now efficiently interact with the Zetrix blockchain using
  the Zetrix Go SDK, providing a convenient and powerful way to build
  blockchain-based applications.
---

# Go

## Overview

The **Zetrix Go SDK** equips developers with a powerful and intuitive toolkit to seamlessly interact with the Zetrix blockchain. It abstracts complex blockchain operations, enabling you to concentrate on building innovative applications. With its user-friendly API and rich feature set, the SDK streamlines tasks such as **account management**, **smart contract deployment**, and **transaction creation**.

## Target Audience

The **Zetrix Go SDK** is built for developers with a strong grasp of **Go (Golang)**. Whether you're experienced in blockchain development or just entering the field, the SDK provides a straightforward and accessible way to start building on the Zetrix platform.

## Quick Start Guide

### Installation

Getting started with the Zetrix Go SDK is straightforward. Follow these steps to install the package in your `go.mod` file:

```sh
$ go get github.com/Zetrix-Chain/zetrix-sdk-go-fork
```

Let's create a `main.go` file as your main executable file. Include these in your import statement:

```go
import (
    "github.com/Zetrix-Chain/zetrix-sdk-go-fork/src/model"
    "github.com/Zetrix-Chain/zetrix-sdk-go-fork/src/sdk"
    "fmt" // include the fmt package to print statements easily
)
```

### Prerequisites

* [Zetrix Wallet](https://www.zetrix.com/zetrix-wallet/) installed.&#x20;
* A Zetrix account with sufficient balance. Claim your faucet here.

### Basic Usages

Included in this section are some sample code snippets to help you get started with the SDK.

Create a Zetrix Go SDK instance to begin using the SDK. Under the `main()` function in your `main.go` file, add the following snippet:

If you're using the Zetrix testnet, initialize the SDK with the testnet node URL:

```go
var zetrixSDK sdk.Sdk
var reqData model.SDKInitRequest
reqData.SetUrl("https://test-node.zetrix.com")
resData := zetrixSDK.Init(reqData)
if resData.ErrorCode != 0 {
	fmt.Println(resData.ErrorDesc)
} else {
	fmt.Println("ZetrixSDK initialized.")
}
```

If you're using Zetrix mainnet, initialize the SDK with the mainnet node URL:

```go
var zetrixSDK sdk.Sdk
var reqData model.SDKInitRequest
reqData.SetUrl("https://node.zetrix.com")
resData := zetrixSDK.Init(reqData)
if resData.ErrorCode != 0 {
	fmt.Println(resData.ErrorDesc)
} else {
	fmt.Println("ZetrixSDK initialized.")
}
```

Now, let's look at some simple examples.

Getting the nonce for a particular address:

```go
var reqData model.AccountGetNonceRequest
reqData.SetAddress(address) // address in a string
resData := zetrixSDK.Account.GetNonce(reqData)
if resData.ErrorCode != 0 {
    return resData.ErrorDesc
} else {
    return resData.Result.Nonce
}
```

Submitting a transaction:

```go
var reqData model.TransactionSubmitRequest
reqData.SetBlob(txBlob) // txBlob in a string
reqData.SetSignatures(signedBlob) // signedBlob in a string
resDataSubmit := zetrixSDK.Transaction.Submit(reqData)
if resDataSubmit.ErrorCode != 0 {
    return resDataSubmit.ErrorDesc
} else {
    return resDataSubmit.Result.Hash
}
```

Getting the balance of a particular address:

```go
var reqData model.AccountGetBalanceRequest
reqData.SetAddress(address) // address in a string
resData := zetrixSDK.Account.GetBalance(reqData)
if resData.ErrorCode != 0 {
    return resData.ErrorDesc
} else {
    return resData.Result.Balance
}
```

Getting transaction info:

```go
var reqData model.TransactionGetInfoRequest
reqData.SetHash(hash) // hash in a string
resData := zetrixSDK.Transaction.GetInfo(reqData)
if resData.ErrorCode != 0 {
    return resData.ErrorDesc
} else {
    return resData.Result
}
```



## API References

### Account APIs

```go
sdk.Account.CheckValid(reqData model.AccountCheckValidRequest)
```

* Purpose: Checks the validity of a given account address.
* Parameters: `reqData` (type: model.AccountCheckValidRequest) - Contains the account address to be validated.
* Return Value: `model.AccountCheckValidResponse` - Contains a boolean indicating if the address is valid and an error code.

```go
sdk.Account.Create()
```

* Purpose: Creates a new account with a public and private key pair.
* Parameters: None.
* Return Value: `model.AccountCreateResponse` - Contains the public key, private key, address, and an error code.

```go
sdk.Account.GetInfo(reqData model.AccountGetInfoRequest)
```

* Purpose: Retrieves information about an account.
* Parameters: `reqData` (type: model.AccountGetInfoRequest) - Contains the account address for which information is requested.
* Return Value: `model.AccountGetInfoResponse` - Contains account information and an error code.

```go
sdk.Account.GetNonce(reqData model.AccountGetNonceRequest)
```

* Purpose: Retrieves the nonce for a given account.
* Parameters: `reqData` (type: model.AccountGetNonceRequest) - Contains the account address for which the nonce is requested.
* Return Value: `model.AccountGetNonceResponse` - Contains the nonce and an error code.

```go
sdk.Account.GetBalance(reqData model.AccountGetBalanceRequest)
```

* Purpose: Retrieves the balance of a given account.
* Parameters: `reqData` (type: model.AccountGetBalanceRequest) - Contains the account address for which the balance is requested.
* Return Value: `model.AccountGetBalanceResponse` - Contains the balance and an error code.

```go
sdk.Account.GetAssets(reqData model.AccountGetAssetsRequest)
```

* Purpose: Retrieves the assets of a given account.
* Parameters: `reqData` (type: model.AccountGetAssetsRequest) - Contains the account address for which assets are requested.
* Return Value: `model.AccountGetAssetsResponse` - Contains the assets and an error code.

```go
sdk.Account.GetMetadata(reqData model.AccountGetMetadataRequest)
```

* Purpose: Retrieves metadata associated with a given account.
* Parameters: `reqData` (type: model.AccountGetMetadataRequest) - Contains the account address and key for which metadata is requested.
* Return Value: `model.AccountGetMetadataResponse` - Contains the metadata and an error code.

```go
sdk.Account.CheckActivated(reqData model.AccountCheckActivatedRequest)
```

* Purpose: Checks if an account is activated.
* Parameters: `reqData` (type: model.AccountCheckActivatedRequest) - Contains the account address to be checked.
* Return Value: `model.AccountCheckActivatedResponse` - Contains a boolean indicating if the account is activated and an error code.

### Contract APIs

```go
sdk.Contract.CheckValid(reqData model.ContractCheckValidRequest)
```

* Purpose: Checks the validity of a contract address.
* Parameters: `reqData` (type: model.ContractCheckValidRequest) - Contains the contract address to be validated.
* Return Value: `model.ContractCheckValidResponse` - Contains a boolean indicating if the contract address is valid and an error code.

```go
sdk.Contract.GetInfo(reqData model.ContractGetInfoRequest)
```

* Purpose: Retrieves information about a contract.
* Parameters: `reqData` (type: model.ContractGetInfoRequest) - Contains the contract address for which information is requested.
* Return Value: `model.ContractGetInfoResponse` - Contains contract information and an error code.

```go
sdk.Contract.Call(reqData model.ContractCallRequest)
```

* Purpose: Calls a contract with specified parameters.
* Parameters: `reqData` (type: model.ContractCallRequest) - Contains details such as contract address, code, input, balance, fee limit, gas price, operation type, and source address.
* Return Value: `model.ContractCallResponse` - Contains the result of the contract call and an error code.

```go
sdk.Contract.GetAddress(reqData model.ContractGetAddressRequest)
```

* Purpose: Retrieves the contract address from a transaction hash.
* Parameters: `reqData` (type: model.ContractGetAddressRequest) - Contains the transaction hash.
* Return Value: `model.ContractGetAddressResponse` - Contains contract address information and an error code.

### Transaction APIs

```go
sdk.Transaction.BuildBlob(reqData model.TransactionBuildBlobRequest)
```

* Purpose: Constructs a transaction blob from the provided transaction details.
* Parameters: `reqData` (type: model.TransactionBuildBlobRequest) - Contains transaction details such as source address, nonce, operations, etc.
* Return Value: `model.TransactionBuildBlobResponse` - Contains the transaction blob and an error code.

```go
sdk.Transaction.EvaluateFee(reqData model.TransactionEvaluateFeeRequest)
```

* Purpose: Estimates the fee required for a transaction based on its details.
* Parameters: `reqData` (type: model.TransactionEvaluateFeeRequest) - Contains transaction details such as source address, nonce, operations, etc.
* Return Value: `model.TransactionEvaluateFeeResponse` - Contains the estimated fee limit, gas price, and an error code.

```go
sdk.Transaction.Sign(reqData model.TransactionSignRequest)
```

* Purpose: Signs a transaction blob using the provided private keys.
* Parameters:
* reqData (type: model.TransactionSignRequest): Contains the transaction blob and private keys for signing.
* Return Value: `model.TransactionSignResponse` - Contains the signatures and an error code.

```go
sdk.Transaction.Submit(reqData model.TransactionSubmitRequest)
```

* Purpose: Submits a signed transaction to the blockchain network.
* Parameters: `reqData` (type: model.TransactionSubmitRequest) - Contains the transaction blob and signatures.
* Return Value: `model.TransactionSubmitResponse` - Contains the transaction hash and an error code.

```go
sdk.Transaction.GetInfo(reqData model.TransactionGetInfoRequest)
```

* Purpose: Retrieves information about a transaction using its hash.
* Parameters: `reqData` (type: model.TransactionGetInfoRequest) - Contains the transaction hash.
* Return Value: `model.TransactionGetInfoResponse` - Contains transaction details and an error code.

### Block APIs

```go
sdk.Block.GetNumber()
```

* Purpose: Retrieves the current block number from the blockchain.
* Parameters: None.
* Return Value: `model.BlockGetNumberResponse` - Contains the block number and an error code.

```go
sdk.Block.CheckStatus()
```

* Purpose: Checks if the blockchain is synchronized.
* Parameters: None.
* Return Value: `model.BlockCheckStatusResponse` - Contains a boolean indicating synchronization status and an error code.

```go
sdk.Block.GetTransactions(reqData model.BlockGetTransactionRequest)
```

* Purpose: Retrieves transactions for a specific block number.
* Parameters: `reqData` (type: model.BlockGetTransactionRequest) - Contains the block number.
* Return Value: `model.BlockGetTransactionResponse` - Contains transaction details and an error code.

```go
sdk.Block.GetInfo(reqData model.BlockGetInfoRequest)
```

* Purpose: Retrieves information about a specific block.
* Parameters: `reqData` (type: model.BlockGetInfoRequest) - Contains the block number.
* Return Value: `model.BlockGetInfoResponse` - Contains block information and an error code.

```go
sdk.Block.GetLatest()
```

* Purpose: Retrieves information about the latest block.
* Parameters: None.
* Return Value: `model.BlockGetLatestResponse` - Contains the latest block information and an error code.

```go
sdk.Block.GetValidators(reqData model.BlockGetValidatorsRequest)
```

* Purpose: Retrieves validators for a specific block number.
* Parameters: `reqData` (type: model.BlockGetValidatorsRequest) - Contains the block number.
* Return Value: `model.BlockGetValidatorsResponse` - Contains validator information and an error code.

```go
sdk.Block.GetLatestValidators()
```

* Purpose: Retrieves validators for the latest block.
* Parameters: None.
* Return Value: `model.BlockGetLatestValidatorsResponse` - Contains the latest validator information and an error code.

```go
sdk.Block.GetReward(reqData model.BlockGetRewardRequest)
```

* Purpose: Retrieves reward distribution for a specific block number.
* Parameters: `reqData` (type: model.BlockGetRewardRequest) - Contains the block number.
* Return Value: `model.BlockGetRewardResponse` - Contains reward distribution details and an error code.

```go
sdk.Block.GetLatestReward()
```

* Purpose: Retrieves the latest reward distribution.
* Parameters: None.
* Return Value: `model.BlockGetLatestRewardResponse` - Contains the latest reward distribution details and an error code.

```go
sdk.Block.GetFees(reqData model.BlockGetFeesRequest)
```

* Purpose: Retrieves fee information for a specific block number.
* Parameters: `reqData` (type: model.BlockGetFeesRequest) - Contains the block number.
* Return Value: `model.BlockGetFeesResponse` - Contains fee information and an error code.

```go
sdk.Block.GetLatestFees()
```

* Purpose: Retrieves the latest fee information.
* Parameters: None.
* Return Value: `model.BlockGetLatestFeesResponse` - Contains the latest fee information and an error code.

### For code references on Zetrix's Go SDK, please visit our [GitHub repository](https://github.com/armmarov/zetrix-sdk-go-fork).
