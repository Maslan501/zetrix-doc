# Legal Evidence

## Zetrix Legal Evidence Example

### Introduction to Zetrix Development

**Zetrix Chain** is a next-generation public blockchain tailored for commercial use. It serves as a global Internet infrastructure for value exchange, built on innovative core technologies such as **Gas Firework**, **Gas Orbits**, **Gas Canal**, and developer-centric smart contracts (**Gas CodeMach**).

To support global development, Zetrix offers:

* Comprehensive **technical documentation**
* Robust **development tools**
* A clear guide to its **technical architecture and ecosystem**

These resources aim to enhance developer productivity and simplify integration with Zetrix’s ecosystem. The platform invites talented developers, industry professionals, and thought leaders worldwide to become part of the **Zetrix developer community**, forming the foundation of its global ecosystem expansion.

This document serves as a practical guide for developers to:

* Access the **Zetrix public blockchain**
* Create **blockchain accounts**
* Issue **digital assets**
* Enable **free asset movement** within the Zetrix network.

The following uses Java development as an example.

### Instantiating the SDK

> **Note**: Complete the basic configuration of the SDK before using it, such as setting the node for the SDK to access.

The example is as follows:

```java
String url = "http://seed1-node.zetrix.com";
SDK sdk = SDK.getInstance(url);

The test network URL：http://seed1-node.zetrix.com
```

### Creating Accounts

> **Note**: You can create an account with the development toolkit provided by the development kit.

Example code:

```java
public static AccountCreateResult createAccount() {
   AccountCreateResponse response = sdk.getAccountService().create();
   if (response.getErrorCode() != 0) {
       return null;
   }
   return response.getResult();
}
```

Return value:

```java
AccountCreateResult

// privateKey     The private key of the account
// privbrosWxhfefT344E943xw2tnsiKVmuvG8hBHNFuxPZg7eHiMBPU6n

// publicKey   The private key of the account b00147b7c959f969079468e60c8f2d41f442b0f6a7b2d765d53e1a6fb272d496b2ebbe1158b5

// address     The account address
// ztxSnVDvg3gfW8as4BeodEJDDQAMXu9NobyvN
```

> **Note**:
>
> * An account created in this mode is **inactive** (not yet written to the blockchain) and therefore **cannot be located** or queried on the Zetrix blockchain until it becomes active through a transaction.
> * An account refers to a randomly generated digital identity of a user, item, institution, house, file, asset issuer account, etc. based on a cryptographic algorithm, usually called a blockchain account (public-private pair and blockchain account address). It is not controlled by any centralized organization and is completely controlled by the account owner.

### &#x20;<a href="#activating-accounts" id="activating-accounts"></a>

### Activating Accounts

> **Note**:
>
> 1. An inactive account requires an account on the chain (an account that can be queried in Zetrix) to activate it.
> 2. Gas is a built-in token on Zetrix, and the transaction on the chain needs to consume Gas.
> 3. If you transfer the Gas of an account who is activated to target the account who is not activated, the target account will be activated.

Example code:

```java
/**
 * Activate a new account
 */
@Test
public void activateAccount() {
    // The account private key to activate a new account
    String activatePrivateKey = "privbyQCRp7DLqKtRFCqKQJr81TurTqG6UKXMMtGAmPG3abcM9XHjWvq";
    Long initBalance = ToBaseUnit.ToUGas("1000");
    // The fixed write 1000L, the unit is UGas
    Long gasPrice = 1000L;
    // Set up the maximum cost 0.01Gas
    Long feeLimit = ToBaseUnit.ToUGas("0.01");
    // Transaction initiation account's nonce + 1
    Long nonce = 8L;

    // Generate a new account to be activated
    Keypair keypair = Keypair.generator();
    System.out.println(JSON.toJSONString(keypair, true));
    String destAccount = keypair.getAddress();

    // 1. Get the account address to send this transaction
    String activateAddresss = getAddressByPrivateKey(activatePrivateKey);

    // 2. Build activateAccount
    AccountActivateOperation operation = new AccountActivateOperation();
    operation.setSourceAddress(activateAddresss);
    operation.setDestAddress(destAccount);
    operation.setInitBalance(initBalance);
    operation.setMetadata("activate account");

    String[] signerPrivateKeyArr = {activatePrivateKey};
    // Record txhash for subsequent confirmation of the real result of the transaction.
    // After recommending five blocks, call again through txhash `Get the transaction information
    // from the transaction Hash'(see example: getTxByHash ()) to confirm the final result of the transaction
    String txHash = submitTransaction(signerPrivateKeyArr, activateAddresss, operation, nonce, gasPrice, feeLimit);
    if (txHash != null) {
        System.out.println("hash: " + txHash);
    }
}
```

### Recording Evidence Information

> **Note**: The evidence information on the blockchain will be permanently stored

#### Obtaining the Account Nonce Value

> **Note**: Each account maintains its own serial number, which starts from 1 and is incremented. A serial number marks a transaction for the account.

Example code:

```java
public long getAccountNonce() {
   long nonce = 0;
   // Init request
   String accountAddress = [Account address of the issuer];
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

Return value:

```java
nonce: 28
```

#### Assembling the Operation for Storing Evidence

Example code:

```java
 /**
     * Assembling the Operation for Storing Evidence
     * @param txOriginatorAddress The initiator's account address
     * @param evidenceKey Evidence KEY
     * @param evidenceInfo Evidence info
     * @param txFee Blockchain transaction fee,in Gas, Note: The more storage content, the more cost you need
     * @return 
     */
    public AccountSetMetadataOperation buildStoreEvidence(String txOriginatorAddress,String evidenceKey,String evidenceInfo){
        //Assemble the evidence data of the transactions
        // The evidence key facilitate retrieval of evidence information
        String key = evidenceKey;
        // Evidence content
        String value = evidenceInfo;

        AccountSetMetadataOperation operation = new AccountSetMetadataOperation();
        operation.setSourceAddress(txOriginatorAddress);
        operation.setKey(key);
        operation.setValue(value);
        return operation;
    }
```

#### &#x20;<a href="#serializing-transactions" id="serializing-transactions"></a>

#### Serializing Transactions

> **Note**: Serialize transactions for network transmission.

Example code:

```java
public String seralizeTransaction(String txOriginatorAddress,  BaseOperation[] operations,String txFee) {
    String transactionBlob = null;

    // The unit price of the transaction fee (transaction is charged in bytes, the unit is UGas, 1 Gas = 10^8 UGas)
    Long gasPrice = 1000L;
    // Transaction cost (in UGas, 1 Gas = 10^8 UGas)
    Long feeLimit = ToBaseUnit.ToUGas(txFee);


    //1. Get the memory account nonce
    Long txOriginatorNonce = getAccountNonce(txOriginatorAddress);

    // Nonce plus 1 as the block transaction number
    txOriginatorNonce += 1;

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

// Note:
// txFee: The transaction fee for this transaction, the more the certificate content, the more the cost
```

Return value:

```java
transactionBlob:

 0A2462755173757248314D34726A4C6B666A7A6B7852394B584A366A537532723978424E4577101C18C0F1CED
 11220E8073A350802122462755173757248314D34726A4C6B666A7A6B7852394B584A366A537532723978424E
 45772A0B0A03474C41108094EBDC033AB6010804122462755173757248314D34726A4C6B666A7A6B7852394B5
 84A366A537532723978424E45773A8B010A1261737365745F70726F70657274795F474C4112757B22636F6465
 223A22474C41222C22746F74616C537570706C79223A313030303030303030302C22646563696D616C73223A3
 02C226E616D65223A22474C41222C2269636F6E223A22222C226465736372697074696F6E223A22474C412054
 4F4B454E222C2276657273696F6E223A22312E30227D
 
```

#### Signing Transactions

> **Note**: After the transaction is serialized, the transaction initiator needs to identify the transaction data (sign with the private key), usually called the signature of the data. The signature results include signature data and a public key.

Example code:

```java
public Signature signTransaction(String transactionBlob,String accountPrivateKey) {
   Signature[] signatures = null;
   String senderPrivateKey = accountPrivateKey;

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
    return signatures[0];
}
```

Return value:

```java
// (signatures)

// signData:  Blob
// 6CGas42B11253BD49E7F1A0A90EB16448C6BC35E8684588DAB8C5D77B5E771BD5C7E1718942B32F9BDE14551866C00FEBA832D92F88755226434413F98E5A990C;

// publicKey: Account public key (transaction initiator's account public key)
// b00179b4adb1d3188aa1b98d6977a837bd4afdbb4813ac65472074fe3a491979bf256ba63895
```

#### Submitting Transactions

> **Note**:
>
> 1. After the transaction data is signed, the transaction metadata (transactionBlob) and signatures (transaction broadcast) need to be submitted (transaction broadcast) to the blockchain network.
> 2. When the transaction involves multiple parties (multiple signature accounts), multiple signatures must be submitted to the Zetrix network when submitting the transaction.

Example code:

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
    return  hash ;
}
```

Return value:

```java
// hash: Transaction hash
// 031fa9a7da6cf8777cdd55df782713d4d05e2465146a697832011b058c0a0cd8
```

> **Note**:
>
> 1. When a transaction involves multiple parties (multiple signature accounts), multiple signatures must be submitted to the Zetrix network when submitting the transaction.
> 2. After submitting the transaction (broadcast transaction), the final result of the transaction can be obtained in about 10 seconds. The developer can query the final status by using the query interface of transaction status.

#### Querying Transaction Status

> **Note**: The final state of the query transaction can be queried through this interface. Usually, the final result of the transaction can be obtained 10 seconds after the transaction is submitted.

Example code:

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

Return value: 0

| Error code | Description                                                                                                                                          |
| ---------- | ---------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0          | Successful operation                                                                                                                                 |
| 1          | Inner service defect                                                                                                                                 |
| 2          | Parameters error                                                                                                                                     |
| 3          | Objects already exist, such as repeated transactions\*\*\*                                                                                           |
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
| 105        | Amount of assets exceeds the limitation\*\*\* ( int64 )                                                                                              |
| 106        | Insufficient initial reserve for account creation                                                                                                    |
| 111        | Low transaction fee                                                                                                                                  |
| 114        | TX buffer is full                                                                                                                                    |
| 120        | Invalid weight                                                                                                                                       |
| 121        | Invalid threshold                                                                                                                                    |
| 144        | Invalid data version of metadata                                                                                                                     |
| 146        | Exceeds upper limitation                                                                                                                             |
| 151        | Failure in contract execution                                                                                                                        |
| 152        | Failure in syntax analysis                                                                                                                           |
| 153        | The depth of contract recursion exceeds upper limitation                                                                                             |
| 154        | The TX submitted from the contract exceeds upper limitation                                                                                          |
| 155        | Contract expired                                                                                                                                     |
| 160        | Fail to insert the TX into buffer                                                                                                                    |
| 11060      | BlockNumber must be bigger than 0                                                                                                                    |
| 11007      | Failed to connect to the network                                                                                                                     |
| 12001      | Request parameter cannot be null                                                                                                                     |
| 20000      | System error                                                                                                                                         |
