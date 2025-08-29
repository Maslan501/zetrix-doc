---
description: >-
  Java developers can easily interact with the Zetrix blockchain using the
  Zetrix Java SDK, offering a streamlined and efficient way to build
  blockchain-enabled applications.
---

# Java

## Overview

The **Zetrix Java SDK** enables developers to interact seamlessly with the Zetrix blockchain through a high-level, intuitive API. This powerful toolkit abstracts the complexities of blockchain operations, allowing you to focus on innovation. It streamlines tasks such as **account management**, **smart contract deployment**, **transaction creation**, **block data retrieval**, and more.

## Target Audience

The **Zetrix Java SDK** is built for developers with a solid background in **Java**, especially those aiming to develop API services that interact with the Zetrix blockchain. Whether you're a veteran in blockchain or just getting started, the SDK provides an approachable and efficient path to building on Zetrix.

## Quick Start Guide

### Installation

Getting started with the Zetrix Java SDK is straightforward. In this tutorial we will be using Maven for dependency management. Follow these steps to include the [package](http://maven.bubidev.cn/#browse/browse:releases:org%2Fzetrix%2Fsdk) in your `pom.xml`.

The repositories required:

```xml
<repositories>
	<repository>
		<id>bubi-release</id>
		<name>releases</name>
		<url>http://maven.bubidev.cn/repository/releases</url>
	</repository>
	<repository>
		<id>bubi-snapshot</id>
		<name>snapshots</name>
		<url>http://maven.bubidev.cn/repository/snapshots</url>
	</repository>
	<repository>
		<id>bubi-thirdparty</id>
		<name>thirdparty</name>
		<url>http://maven.bubidev.cn/repository/thirdparty</url>
	</repository>
	<repository>
		<id>danubetech-maven-public</id>
		<url>https://repo.danubetech.com/repository/maven-public/</url>
	</repository>
</repositories>
```

The dependencies required:

```xml
<dependencies>
	<dependency>
		<groupId>org.zetrix.sdk</groupId>
		<artifactId>zetrix-sdk</artifactId>
		<version>1.0.5.RELEASE</version>
	</dependency>
	<dependency>
		<groupId>org.zetrix.encryption</groupId>
		<artifactId>zetrix-encryption</artifactId>
		<version>1.0.0.RELEASE</version>
	</dependency>
	<dependency>
		<groupId>com.alibaba</groupId>
		<artifactId>fastjson</artifactId>
		<version>2.0.21</version>
	</dependency>
	<dependency>
		<groupId>net.i2p.crypto</groupId>
		<artifactId>eddsa</artifactId>
		<version>0.1.0</version>
	</dependency>
</dependencies>
```

Do note that the individual environments may differ. Please create an issue [here](https://github.com/Zetrix-Chain/zetrix-sdk-java/issues) if you face any problems setting up your environment for development.

### Prerequisites

* ​[Zetrix Wallet](https://www.zetrix.com/zetrix-wallet/) installed.
* A Zetrix account with sufficient balance. Claim your faucet [here](https://faucet.zetrix.com/).

### Basic Usages <a href="#basic-usages" id="basic-usages"></a>

Included in this section are some sample code snippets to help you get started with the SDK.&#x20;

Create a Zetrix Java SDK instance to begin using the SDK.

If you're using the Zetrix testnet, initialize with this URL:&#x20;

```java
// Testnet node URL
SDK sdk = SDK.getInstance("https://test-node.zetrix.com");
```

If you're using the Zetrix mainnet, initialize with this URL:

```java
// Mainnet node URL
SDK sdk = SDK.getInstance("https://node.zetrix.com");
```

Let's look at some examples:

Getting the nonce for a particular address:

```java
AccountGetNonceRequest request = new AccountGetNonceRequest();
request.setAddress(address);
AccountGetNonceResponse response = sdk.getAccountService().getNonce(request);
return response.getResult().getNonce();
```

Submitting a transaction:

```java
TransactionSubmitRequest transactionSubmitRequest = new TransactionSubmitRequest();
transactionSubmitRequest.setTransactionBlob(submitTxReq.getBlob());
transactionSubmitRequest.setSignatures(signatures);
return sdk.getTransactionService().submit(transactionSubmitRequest);
```

Getting the balance of a particular address:

```java
AccountGetBalanceResponse response = sdk.getAccountService().getBalance(request);
return response.getResult().getBalance();
```

Getting transaction info:

```java
TransactionGetInfoRequest txInfo = new TransactionGetInfoRequest();
txInfo.setHash(txHash);
TransactionGetInfoResponse respInfo = sdk.getTransactionService().getInfo(txInfo);
TransactionHistory txHistory = respInfo.getResult().getTransactions()[0];
```



## API References

### Account APIs

```java
sdk.getAccountService().getNonce(AccountGetNonceRequest request);
```

* Purpose: Retrieves the nonce of an account.
* Parameters: `AccountGetNonceRequest` - Contains the address of the account to retrieve the nonce for.
* Return: `AccountGetNonceResponse` - The response object containing the account's nonce.

```java
sdk.getAccountService().getMetadata(AccountGetMetadataRequest request);
```

* Purpose: Retrieves metadata for an account.
* Parameters: `AccountGetMetadataRequest` - Contains the address and key of the metadata to retrieve.
* Return: `AccountGetMetadataResponse` - The response object containing the account's metadata.

```java
sdk.getAccountService().getBalance(AccountGetBalanceRequest request);
```

* Purpose: Retrieves the balance of an account.
* Parameters: `AccountGetBalanceRequest` - Contains the address of the account to retrieve the balance for.
* Return: `AccountGetBalanceResponse` - The response object containing the account's balance.

```java
sdk.getAccountService().getInfo(AccountGetInfoRequest request);
```

* Purpose: Retrieves information about an account.
* Parameters: `AccountGetInfoRequest` - Contains the address of the account to retrieve information for.
* Return: `AccountGetInfoResponse` - The response object containing the account information.

```java
sdk.getAccountService().checkValid(AccountCheckValidRequest request);
```

* Purpose: Checks if an account address is valid.
* Parameters: `AccountCheckValidRequest` - Contains the address to be validated.
* Return: `AccountCheckValidResponse` - The response object indicating whether the address is valid.

```java
sdk.getAccountService().create()
```

* Purpose: Creates a new account by generating a new private key, public key, and address.
* Parameters: None.
* Return: `AccountCreateResponse` - The response object containing the new account's private key, public key, and address.

```java
sdk.getAccountService().checkActivated(AccountCheckActivatedRequest request);
```

* Purpose: Checks if an account is activated.
* Parameters: `AccountCheckActivatedRequst` - Contains the address of the account to check.
* Return: `AccountCheckActivatedResponse` - The response object indicating whether the account is activated.

### Block APIs

```java
sdk.getBlockService().getNumber();
```

* Purpose: Retrieves the current block number from the blockchain.
* Parameters: None.
* Return: `BlockGetNumberResponse` - Contains the block number and any error information if applicable.

```java
sdk.getBlockService().getInfo(BlockGetInfoRequest request);
```

* Purpose: Retrieves information about a specific block.
* Parameters: `BlockGetInfoRequest` - Contains the block number for which information is requested.
* Return: `BlockGetInfoResponse` - Contains block information and any error information.

```java
sdk.getBlockService().getTransactions(BlockGetTransactionsRequest request);
```

* Purpose: Retrieves transactions for a specific block number.
* Parameters: `BlockGetTransactionsRequest` - Contains the block number for which transactions are requested.
* Return: `BlockGetTransactionsResponse` - Contains transaction details and any error information.

```java
sdk.getBlockService().checkStatus();
```

* Purpose: Checks the synchronization status of the blockchain.
* Parameters: None.
* Return: `BlockCheckStatusResponse` - Indicates whether the blockchain is synchronized and includes any error information.

```java
sdk.getBlockService().getLatestInfo();
```

* Purpose: Retrieves information about the latest block.
* Parameters: None.
* Return: `BlockGetLatestInfoResponse` - Contains the latest block information and any error information.

```java
sdk.getBlockService().getValidators(BlockGetValidatorsRequest request);
```

* Purpose: Retrieves validator information for a specific block.
* Parameters: `BlockGetValidatorsRequest` - Contains the block number for which validator information is requested.
* Return: `BlockGetValidatorsResponse` - Contains validator details and any error information.

```java
sdk.getBlockService().getLatestValidators();
```

* Purpose: Retrieves information about the latest validators.
* Parameters: None.
* Return: `BlockGetLatestValidatorsResponse` - Contains the latest validator information and any error information.

```java
sdk.getBlockService().getReward(BlockGetRewardRequest request);
```

* Purpose: Retrieves reward distribution information for a specific block.
* Parameters: `BlockGetRewardRequest` - Contains the block number for which reward information is requested.
* Return: `BlockGetRewardResponse` - Contains reward distribution details and any error information.

```java
sdk.getBlockService().getLatestReward();
```

* Purpose: Retrieves the latest reward distribution information.
* Parameters: None.
* Return: `BlockGetLatestRewardResponse` - Contains the latest reward distribution details and any error information.

```java
sdk.getBlockService().getFees(BlockGetFeesRequest request);
```

* Purpose: Retrieves fee information for a specific block.
* Parameters: `BlockGetFeesRequest` - Contains the block number for which fee information is requested.
* Return: `BlockGetFeesResponse` - Contains fee details and any error information.

```java
sdk.getBlockService().getLatestFees();
```

* Purpose: Retrieves the latest fee information.
* Parameters: None.
* Return: `BlockGetLatestFeesResponse` - Contains the latest fee details and any error information.



### Transaction APIs

```java
sdk.getTransactionService().buildBlob(TransactionBuildBlobRequest request);
```

* Purpose: Constructs a transaction blob from the given request, which includes validating the request parameters and building the transaction.
* Parameters: `TransactionBuildBlobRequest` - Contains details like source address, nonce, gas price, fee limit, metadata, and operations.
* Return: `TransactionBuildBlobResponse` - Contains the transaction blob and its hash if successful, or error details if an exception occurs.

```java
sdk.getTransactionService().parseBlob(TransactionParseBlobRequest request);
```

* Purpose: Parses a transaction blob into a readable format, converting it to JSON.
* Parameters: `TransactionParseBlobRequest` - Contains the transaction blob to be parsed.
* Return: `TransactionParseBlobResponse` - Contains the parsed transaction details in JSON format or error details if an exception occurs.

```java
sdk.getTransactionService().sign(TransactionSignRequest request);
```

* Purpose: Signs a transaction blob using the provided private keys.
* Parameters: `TransactionSignRequest` - Contains the transaction blob and an array of private keys for signing.
* Return: `TransactionSignResponse` - Contains the signatures and public keys if successful, or error details if an exception occurs.

```java
sdk.getTransactionService().submit(TransactionSubmitRequest request);
```

* Purpose: Submits a signed transaction to the blockchain network.
* Parameters: `TransactionSubmitRequest` - Contains the transaction blob and signatures.
* Return: `TransactionSubmitResponse` - Contains the transaction hash if successful, or error details if an exception occurs.

```java
sdk.getTransactionService().getInfo(TransactionGetInfoRequest request);
```

* Purpose: Retrieves information about a transaction using its hash.
* Parameters: `TransactionGetInfoRequest` - Contains the hash of the transaction to be queried.
* Return: `TransactionGetInfoResponse` - Contains transaction details or error information if an exception occurs.

```java
sdk.getTransactionService().evaluateFee(TransactionEvaluateFeeRequest request);
```

* Purpose: Evaluates the fee required for a transaction based on the provided request details.
* Parameters: `TransactionEvaluateFeeRequest` - Includes source address, nonce, signature number, metadata, and operations.
* Return: `TransactionEvaluateFeeResponse` - Contains the evaluated fee details or error information if an exception occurs.



### Contract APIs

```java
sdk.getContractService().call(ContractCallRequest request);
```

* Purpose: Calls a contract with specified parameters.
* Parameters: `ContractCallRequest` - An object containing details like source address, contract address, code, input, contract balance, gas price, and fee limit.
* Return: `ContractCallResponse` - The result of the contract call or error details if the call fails.

```java
sdk.getContractService().getInfo(ContractGetInfoRequest request);
```

* Purpose: Retrieves information about a specific contract using its address.
* Parameters: `ContractGetInfoRequest` - An object containing the contract address.
* Return: `ContractGetInfoResponse` - Contains the contract information or error details if the request fails.

```java
sdk.getContractService().checkValid(ContractCheckValidRequest request);
```

* Purpose: Checks if a given contract address is valid.
* Parameters: `ContractCheckValidRequest` - An object containing the contract address to be validated.
* Return: `ContractCheckValidResponse` - Indicates whether the contract address is valid or not.

```java
sdk.getContractService().getAddress(ContractGetAddressRequest request);
```

* Purpose: Retrieves the contract address associated with a given transaction hash.
* Parameters: `ContractGetAddressRequest` - An object containing the transaction hash.
* Return: `ContractGetAddressResponse` - Containing the contract address information or error details if the retrieval fails.

### For code references on Zetrix's Java SDK, please visit our [GitHub repository](https://github.com/Zetrix-Chain/zetrix-sdk-java).
