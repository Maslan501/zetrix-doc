# Asset Issuance

## Zetrix Asset Issuance Example

### Scenario Description

The token issuer issues **1,000,000,000 GLA (Global)** assets on **Zetrix** with the token code **GLA** and name **Global**.

| Field Name  | Required | Example    | Description            |
| ----------- | -------- | ---------- | ---------------------- |
| name        | yes      | Global     | Token name             |
| code        | yes      | GLA        | Token code             |
| totalSupply | yes      | 1000000000 | Total supply of assets |
| decimals    | yes      | 8          | Token decimal          |
| description | no       |            | Token description      |
| icon        | no       |            | Token ICON             |
| version     | yes      | 1.0        | The version number     |

**Explain**:

* **code**: **Capitalized spell is recommended**.
* **decimals**: Defines the number of decimal places; must be in the range of 0 to 8. A value of 0 means the asset has no decimal places.
* **totalSupply**: Specifies the total number of assets to be issued. Must be within 0 to 2⁶³−1. A value of 0 means the asset has no supply limit.
* _Example_: Issuing 10,000 assets with 8 decimal places → `totalSupply = 10,000 × 10⁸ = 1,000,000,000,000`.
* **icon**: Provided as a Base64-encoded image. File size must be under 32 KB, and a 200×200 pixels dimension is recommended.
* **version**: Indicates the protocol version. The current value is 1.0.

### &#x20;<a href="#development-process-for-token-issuing" id="development-process-for-token-issuing"></a>

### Development Process for Token Issuing

In this document, we use **Java** as an example to create a **token issuer** and issue **1,000,000,000 assets** on **Zetrix**.

> **Note**: Please replace the \[AccountAddressOfTokenIssuer] in the examples with the account address of the token to be issued by the token issuer. And replace the \[AccountPrivateKeyOfTokenIssuer] in the examples with the account private key of the token to be issued by the token issuer.

#### &#x20;<a href="#creating-an-sdk-instance" id="creating-an-sdk-instance"></a>

#### Creating an SDK Instance

We create an instance with the following code and set its url (the IP and Port of the node).

```java
 String url = "http://seed1-node.zetrix.com";
 SDK sdk = SDK.getInstance(url);
```

In Zetrix, the generation time for each block is 10 seconds, and only one confirmation is needed for a transaction to get the final state.

The environment description is as follows:

| Network Environment | IP                    |
| ------------------- | --------------------- |
| Zetrix Testnet      | seed1-node.zetrix.com |

If you require ZETRIX coins in the testnet, you may use link below: [https://faucet.zetrix.com/](https://faucet.zetrix.com/)

#### Creating an Account for the Token Issuer

The specific code for creating an account for the token issuer is as follows:

```java
public static AccountCreateResult createAccount() {
    AccountCreateResponse response = sdk.getAccountService().create();
    if (response.getErrorCode() != 0) {
        return null;
    }
    return response.getResult();
}
```

The return value is as follows:

```
 AccountCreateResult
     address:  ztxSXVGt5ujjAe4hr11VqhjWpJdKqn6QfDVUX
     privateKey:  privbUAYxPLLyaxvU3EMkSTfuEDTWxAYvyCasUcCgUxDihtNXQL4oHJx
     publicKey: b001724ed9475ca4c8893329924c7dceae66c61d8577ab2c2c3b29376e143137c20a4bbed176
```

> **Note**: The account created above is not activated.

#### Activating the Account of Token Issuer

The non-activated account must be activated by an already activated account.\
You may skip this step if your issuer account is already activated.

> **Note**: You can transfer Gas of the account who is activated to the account who is not activated to activate it.

#### Getting the Nonce Value

Each account maintains a nonce value which starts at 1. The nonce value represents the amount of transactions in the account.

The code used to get the nonce value is as follows:

```java
 public long getAccountNonce() {
    long nonce = 0;

    // Init request
    String accountAddress = [AccountAddressOfTokenIssuer];
    AccountGetNonceRequest request = new AccountGetNonceRequest();
    request.setAddress(accountAddress);

    // Call getNonce
    AccountGetNonceResponse response = sdk.getAccountService().getNonce(request);
    if (0 == response.getErrorCode()) {
    nonce = response.getResult().getNonce();
    } else {
    System.out.println("error: " + response.getErrorDesc());
    }
    return nonce;
  }
```

The return value is as follows:

```
 nonce: 28
```

#### Grouping Oprations for Token Issuing

A transaction can include multiple operations, each representing a specific transaction instruction or content.

Two operations are needed to issue an assets: `AssetIssueOperation`, and `AccountSetMetadataOperation`.

The specific code for grouping operations for token issuing is as follows:

```java
    public BaseOperation[] buildOperations() {
    // The account address to issue apt1.0 token
    String issuerAddress = [AccountAddressOfTokenIssuer];
    // The token name
    String name = "Global";
    // The token code
    String code = "GLA";
    // The apt token version
    String version = "1.0";
    // The apt token icon
    String icon = "";
    // The total supply number of assets
    Long totalSupply = 1000000000L;
    // The present supply number of assets
    Long nowSupply = 1000000000L;
    // The token description
    String description = "GLA TOKEN";
    // The token decimals
    Integer decimals = 0;
    
    // Build token issuance operation
    AssetIssueOperation assetIssueOperation = new AssetIssueOperation();
    assetIssueOperation.setSourceAddress(issuerAddress);
    assetIssueOperation.setCode(code);
    assetIssueOperation.setAmount(nowSupply);
    
    // If this is an asset you must set metadata like this
    JSONObject assetsJson = new JSONObject();
    assetsJson.put("name", name);
    assetsJson.put("code", code);
    assetsJson.put("description", description);
    assetsJson.put("decimals", decimals);
    assetsJson.put("totalSupply", totalSupply);
    assetsJson.put("icon", icon);
    assetsJson.put("version", version);
    
    String key = "asset_property_" + code;
    String value = assetsJson.toJSONString();
    // Build setMetadata
    AccountSetMetadataOperation accountSetMetadataOperation = new AccountSetMetadataOperation();
    accountSetMetadataOperation.setSourceAddress(issuerAddress);
    accountSetMetadataOperation.setKey(key);
    accountSetMetadataOperation.setValue(value);
    
    BaseOperation[] operations = {assetIssueOperation, accountSetMetadataOperation};
    return operations;
    }
```

#### Serializing Transactions

Transactions are serialized for network transmission.

> **Note**:
>
> * feeLimit: The maximum fee the transaction initiator will pay for the transaction, and please fill in 50.03 Gas when the operation is issuing assets.
> * nonce: The nonce value of this transaction initiator, which can be obtained by adding 1 to the current nonce value.

The specific code for serializing transactions is as follows. In the example, `nonce` is the series number of account obtained by calling `getAccountNonce`, and `operations` is the operations for issuing assets obtained by calling `buildOperations`.

```java
 public String seralizeTransaction(Long nonce,  BaseOperation[] operations) {
    String transactionBlob = null;

    // The account address to issue token
    String senderAddresss =[AccountAddressOfTokenIssuer];
    // The gasPrice is fixed at 1000L, the unit is UGas
    Long gasPrice = 1000L;
    // Set up the maximum cost 50.03Gas
    Long feeLimit = ToBaseUnit.ToUGas("50.03");
    // Nonce should add 1
    nonce += 1;

    // Build transaction Blob
    TransactionBuildBlobRequest transactionBuildBlobRequest = new TransactionBuildBlobRequest();
    transactionBuildBlobRequest.setSourceAddress(senderAddresss);
    transactionBuildBlobRequest.setNonce(nonce);
    transactionBuildBlobRequest.setFeeLimit(feeLimit);
    transactionBuildBlobRequest.setGasPrice(gasPrice);
    for (int i = 0; i < operations.length; i++) {
    transactionBuildBlobRequest.addOperation(operations[i]);
    }
    TransactionBuildBlobResponse transactionBuildBlobResponse = sdk.getTransactionService().buildBlob(transactionBuildBlobRequest);
    if (transactionBuildBlobResponse.getErrorCode() == 0) {
        transactionBlob = transactionBuildBlobResponse. getResult().getTransactionBlob();
    } else {
        System.out.println("error: " + transactionBuildBlobResponse.getErrorDesc());
    }
    return transactionBlob;
 }
```

The return value is as follows:

```
 transactionBlob: 
 0A2462755173757248314D34726A4C6B666A7A6B7852394B584A366A537532723978424E4577101C18C0F1CED11220E8073A350802122462755173757248314D34726A4C6B666A7A6B7852394B584A366A537532723978424E45772A0B0A03474C41108094EBDC033AB6010804122462755173757248314D34726A4C6B666A7A6B7852394B584A366A537532723978424E45773A8B010A1261737365745F70726F70657274795F474C4112757B22636F6465223A22474C41222C22746F74616C537570706C79223A313030303030303030302C22646563696D616C73223A302C226E616D65223A22474C41222C2269636F6E223A22222C226465736372697074696F6E223A22474C412054 
```

#### Signing Transactions

All transactions need to be signed to be valid. The signing result includes the signature data and the public key.

The specific code for signing transactions is as follows. In the example, `transactionBlob` is the string of the seralized transactions obtained by calling `seralizeTransaction`.

```java
 public Signature[] signTransaction(String transactionBlob) {
     Signature[] signatures = null;
     // The account private key to issue token
     String senderPrivateKey =[AccountPrivateKeyOfTokenIssuer];
     
    // Sign transaction BLob
    TransactionSignRequest transactionSignRequest = new TransactionSignRequest();
    transactionSignRequest.setBlob(transactionBlob);
    transactionSignRequest.addPrivateKey(senderPrivateKey);
    TransactionSignResponse transactionSignResponse = sdk.getTransactionService().sign(transactionSignRequest);
    if (transactionSignResponse.getErrorCode() == 0) {
        signatures = transactionSignResponse.getResult().getSignatures();
    } else {
        System.out.println("error: " + transactionSignResponse.getErrorDesc());
    }
    return signatures;
 }
```

The return value is as follows:

```
 signData: 6CGas42B11253BD49E7F1A0A90EB16448C6BC35E8684588DAB8C5D77B5E771BD5C7E1718942B32 
 F9BDE14551866C00FEBA832D92F88755226434413F98E5A990C
 publicKey: b00179b4adb1d3188aa1b98d6977a837bd4afdbb4813ac65472074fe3a491979bf256ba63895
```

#### Sending Transactions

Sending transactions refers to sending the serialized transactions and the signatures to Zetrix.

The specific code for sending transactions is as follows. In the example, `transactionBlob` is the string of the seralized transactions obtained by calling seralizeTransaction, and `signatures` is the signature data obtained by calling signTransaction.

```java
 public String submitTransaction(String transactionBlob, Signature[] signatures) {
    String  hash = null;
    
    // Submit transaction
    TransactionSubmitRequest transactionSubmitRequest = new TransactionSubmitRequest();
    transactionSubmitRequest.setTransactionBlob(transactionBlob);
    transactionSubmitRequest.setSignatures(signatures);
    TransactionSubmitResponse transactionSubmitResponse = sdk.getTransactionService().submit(transactionSubmitRequest);
    if (0 == transactionSubmitResponse.getErrorCode()) {
        hash = transactionSubmitResponse.getResult().getHash();
    } else {
        System.out.println("error: " + transactionSubmitResponse.getErrorDesc());
    }
    return  hash;
 }
```

The return value is as follows:

```
 hash:  031fa9a7da6cf8777cdd55df782713d4d05e2465146a697832011b058c0a0cd8
```

#### Checking the Result of the Transaction Execution

> **Note**: The returned result of transactions sent represents whether the transaction is submitted successfully.

The specific code to call the interface is as follows. In the example, `txHash` is the hash value of transactions which is the unique identification obtained by calling submitTransaction.

```java
public int checkTransactionStatus(String txHash) {
    // Init request
    TransactionGetInfoRequest request = new TransactionGetInfoRequest();
    request.setHash(txHash);

    // Call getInfo
    TransactionGetInfoResponse response = sdk.getTransactionService().getInfo(request);
    int errorCode = response.getErrorCode();
    if (errorCode == 0){
        TransactionHistory transactionHistory = response.getResult().getTransactions()[0];
        errorCode = transactionHistory.getErrorCode();
    }

    return errorCode;
 }
```

The return value is as follows: 0

| Error code | Description                                                                                                                                          |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0          | Successful operation                                                                                                                                 |
| 1          | Inner service defect                                                                                                                                 |
| 2          | Parameters error                                                                                                                                     |
| 3          | Objects already exist, such as repeated transactions                                                                                                 |
| 4          | Objects do not exist, such as null account, transactions and blocks etc.                                                                             |
| 5          | Transactions expired. It means the transaction has been removed from the buffer, but it still has probability to be executed.                        |
| 7          | Math calculation is overflown                                                                                                                        |
| 20         | The expression returns false. It means the TX failed to be executed currently, but it still has probability to be executed in the following blocks . |
| 21         | The syntax of the expression returns are false. It means that the TX must fail.                                                                      |
| 90         | Invalid public key                                                                                                                                   |
| 91         | Invalid private key                                                                                                                                  |
| 92         | Invalid assets                                                                                                                                       |
| 93         | The weight of the signature does not match the threshold of the operation.                                                                           |
| 94         | Invalid address                                                                                                                                      |
| 97         | Absent operation of TX                                                                                                                               |
| 98         | Over 100 operations in a single transaction                                                                                                          |
| 99         | Invalid sequence or nonce of TX                                                                                                                      |
| 100        | Low reserve in the account                                                                                                                           |
| 101        | Sender and receiver accounts are the same                                                                                                            |
| 102        | The target account already exists                                                                                                                    |
| 103        | Accounts do not exist                                                                                                                                |
| 104        | Low reserve in the account                                                                                                                           |
| 105        | Amount of assets exceeds the limitation ( int64 )                                                                                                    |
| 106        | Insufficient initial reserve for account creation                                                                                                    |
| 111        | Low transaction fee                                                                                                                                  |
| 114        | TX buffer is full                                                                                                                                    |
| 120        | Invalid weight                                                                                                                                       |
| 121        | Invalid threshold                                                                                                                                    |
| 144        | Invalid data version of metadata                                                                                                                     |
| 146        | exceeds upper limitation                                                                                                                             |
| 151        | Failure in contract execution                                                                                                                        |
| 152        | Failure in syntax analysis                                                                                                                           |
| 153        | The depth of contract recursion exceeds upper limitation                                                                                             |
| 154        | the TX submitted from the contract exceeds upper limitation                                                                                          |
| 155        | Contract expired                                                                                                                                     |
| 160        | Fail to insert the TX into buffer                                                                                                                    |
| 11060      | BlockNumber must bigger than 0                                                                                                                       |
| 11007      | Failed to connect to the network                                                                                                                     |
| 12001      | Request parameter cannot be null                                                                                                                     |
| 20000      | System error                                                                                                                                         |
