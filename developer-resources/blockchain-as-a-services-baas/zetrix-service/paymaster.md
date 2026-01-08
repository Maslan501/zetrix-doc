# Paymaster

## Overview

The Paymaster API enables **gas-sponsored transactions** on the Zetrix blockchain by allowing users to delegate transaction fee payments to a managed Paymaster account.

This API is designed to:

* Remove the requirement for end users to hold ZTX for gas fees.
* Provide secure and fair **paymaster rotation.**
* Ensure correct **nonce management** across concurrent transactions.
* Improve transaction reliability through tracking and recovery mechanisms.

The Paymaster service acts as an intermediary between client applications and the Zetrix blockchain, handling nonce allocation, paymaster selection, transaction submission, and failure recovery.

***

## Transaction Flow

1. **Generate Blob**\
   Client requests the Paymaster API to generate an unsigned transaction blob.
2. **Client Signs Blob**\
   The unsigned blob is signed locally using the user’s private key.
3. **Submit Blob**\
   The signed blob is submitted back to the Paymaster API for paymaster signing and on-chain submission.
4. **Transaction Tracking & Recovery**\
   The system tracks the transaction status and performs automatic recovery if a transaction is dropped or stuck.

## Paymaster API Specification

### Generate Blob

#### Authorization

Authorization must be sent with all client requests. The Bearer Access Token needed to authenticate and authorize user to access the API

#### **Request**

```bash
POST /ztx/tx/paymaster/generate-blob
```

**CURL Command**

```bash
curl POST https://{endpoint}/ztx/tx/paymaster/generate-blob
```

| Type | Params                                                                                                                                                                                                                                                    | Values                                                                                                                                                                                                                                   |
| ---- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| HEAD | `Authorization`                                                                                                                                                                                                                                           | `string`                                                                                                                                                                                                                                 |
| POST | <p><code>operations[]</code></p><ul><li><code>amount</code></li><li><code>inputStr</code></li><li><code>fromAddress</code></li><li><code>type</code></li><li><code>toAddress</code></li><li><code>precision</code></li><li><code>payload</code></li></ul> | <p><code>Array[]</code></p><ul><li><code>String</code></li><li><code>String</code></li><li><code>String</code></li><li><code>String</code></li><li><code>String</code></li><li><code>String</code></li><li><code>String</code></li></ul> |

**amount**

`amount` is amount that need to be transfer to other wallet.

**inputStr**

`inputStr` is general field for memos.

**type**

`type` is type of transaction, below is the list of type:

1. Create Account
2. Transfer
3. Call Contract
4. Create Contract
5. Set Metadata
6. Account Set Privilege
7. Upgrade Contract Content
8. Upgrade Contract Owner

**toAddress**

`toAddress` is wallet account address to receive transactions.

**precision**

`precision` is precision is decimal places for amount transfer, default is 6

**payload**

`precision` is precision is decimal places for amount transfer, default is 6

#### Function&#x20;

To Generate a blob for a given transaction data.

#### Request Body

The body is in json format.

Here body transfer is the transaction data. For specific json format and parameters, see transaction structure. Example：

```json
{
    "operations": [
        {
            "amount": "1",
            "inputStr": "",
            "fromAddress": "ZTX3bt9zo4RUqF4EgVT9mer57qHAbxqyKzzk7",
            "type": "2",
            "toAddress": "ZTX3aUu8KbuMR9N8ATCXtoY8kU8oepRbDChaF",
            "precision": "",
            "payload": ""
        }
    ]
}

```

#### Response

```json
{
    "object": {
        "blob": "0A255A5458336370526E4667693234504D55766E7178434262374C6338724A5A75714563546E5210052256080712255A5458336274397A6F34525571463445675654396D65723537714841627871794B7A7A6B37622B0A255A545833615575384B62754D52394E3841544358746F59386B55386F65705262444368614610C0843D30E45F3805",
        "hash": "94f5edbbb19f0449c317ef38b90490eefd883f886be10afc35ec05393f55dffe",
        "platformSignData": null,
        "paymasterAddress": "ZTX3cpRnFgi24PMUvnqxCBb7Lc8rJZuqEcTnR",
        "nonce": 5
    },
    "messages": []
}

```

### Submit Blob

Submit signed blob for create transaction

#### Authorization

Authorization must be sent with all client requests. The Bearer Access Token needed to authenticate and authorize user to access the API

**Request**

```bash
POST /ztx/tx/paymaster/submit
```

CURL Command

```bash
curl POST https://{endpoint}/ztx/tx/paymaster/submit
```

| Type | Params                                                                                                  | Values                                                                                       |
| ---- | ------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| HEAD | `Authorization`                                                                                         | `String`                                                                                     |
| POST | `blob`                                                                                                  | `String`                                                                                     |
| POST | `hash`                                                                                                  | `String`                                                                                     |
| POST | <p><code>listSigners[]</code></p><ul><li><code>signBlob</code></li><li><code>publicKey</code></li></ul> | <p><code>Array[]</code>  </p><p> <br><code>String]</code></p><p><code>String</code> <br></p> |
| POST | `paymasterAddress`                                                                                      | `String`                                                                                     |

**blob**

`blob` is transaction blob that need to signed

**hash**

`hash` is transaction hash

**signBlob**

`signBlob` is generated blob that have been signed by user wallet private key

**publicKey**

`publicKey` is user wallet public key

**paymasterAddress**

`paymasterAddress` is wallet address of paymaster resulted from /tx/generate-blob

#### Function&#x20;

To submit a blob for execute transaction.

#### Request Body

The body is in json format.

Here body transfer is the transaction data. For specific json format and parameters, see transaction structure. Example：

```json
{
    "blob": "0A255A5458336370526E4667693234504D55766E7178434262374C6338724A5A75714563546E5210092256080712255A5458336274397A6F34525571463445675654396D65723537714841627871794B7A7A6B37622B0A255A545833615575384B62754D52394E3841544358746F59386B55386F65705262444368614610C0843D30E45F3805",
    "hash": "a4e42e4c0f8be1eb089bdc0d9a9a6fe0e465f3e1956771157889bcdd2f767452",
    "listSigners": [
        {
            "signBlob": "25831C2C112F7EEA3AE39FC824A2089033F4E1527CA0C14F1772E42AF451FDA5A0334AC7EB64459B84A089D24D36D2E7FE119ED02BCF46A445FB3650A246A400",
            "publicKey": "b001c867ba4bc9378b8a7bd848b0713847720fcbbe0446737f2172a2d8248cc97934e66464c9"
        }
    ],
    "paymasterAddress": "ZTX3cpRnFgi24PMUvnqxCBb7Lc8rJZuqEcTnR"
}
```

#### Response

```json
{
    "object": {
        "hash": "a4e42e4c0f8be1eb089bdc0d9a9a6fe0e465f3e1956771157889bcdd2f767452",
        "paymasterAddress": "ZTX3cpRnFgi24PMUvnqxCBb7Lc8rJZuqEcTnR",
        "message": "Success"
    },
    "messages": []
}
```

## Paymaster Smart Contract

### Generate Blob

#### Authorization

Authorization must be sent with all client requests. The Bearer Access Token needed to authenticate and authorize user to access the API

**Request**

```bash
POST /ztx/contract/paymaster/generate-blob
```

**CURL Command**

```bash
curl POST https://{endpoint}/ztx/contract/paymaster/generate-blob
```

| Type | Params                                                                                          | Values                                                                                          |
| ---- | ----------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| HEAD | `Authorization`                                                                                 | `String`                                                                                        |
|      | `address`                                                                                       | `String`                                                                                        |
|      | `method`                                                                                        | `String`                                                                                        |
|      | <p><code>inputParameters</code></p><ul><li><code>to</code></li><li><code>value</code></li></ul> | <p><code>Array[]</code><br></p><p><br><br><code>String</code><br></p><p><code>String</code></p> |
|      | `sourceAddress`                                                                                 | `String`                                                                                        |

**address**

`address` is contract address

**method**

`method` is invoke method in smart contract to interact\
\
**inputParameters**

`inputParameters` is input data that required for invoke method

**sourceAddress**

`inputParameters` is owner of smart contract

#### Function&#x20;

To generate a blob from a given transaction data.

#### Request Body

The body is in json format

Here body transfer is the transaction data. For specific json format and parameters, see Transaction Structure. Example：

```json
{
    "address": "ZTX3cjLdUXK9AqQWyLFTT72xmWxVhsGQVyBUV",
    "method": "transfer",
    "inputParameters": {
        "to": "ZTX3aUu8KbuMR9N8ATCXtoY8kU8oepRbDChaF",
        "value": "1000000"
    },
    "sourceAddress": "ZTX3bt9zo4RUqF4EgVT9mer57qHAbxqyKzzk7"
}
```

#### Response

```json
{
    "object": {
        "blob": "0A255A54583364584C6235566A395377344E427973444B556D7238486541586A4564383647456F101522B401080712255A5458336274397A6F34525571463445675654396D65723537714841627871794B7A7A6B376288010A255A545833636A4C6455584B3941715157794C4654543732786D5778566873475156794255561A5F7B226D6574686F64223A227472616E73666572222C22706172616D73223A7B22746F223A225A545833615575384B62754D52394E3841544358746F59386B55386F657052624443686146222C2276616C7565223A2231303030303030227D7D309E693805",
        "hash": "4db408ea17820a985641c7fa5c52cf13d8b1103cc59b36fce38b9458315d7221",
        "platformSignData": null,
        "paymasterAddress": "ZTX3dXLb5Vj9Sw4NBysDKUmr8HeAXjEd86GEo",
        "nonce": 21
    },
    "messages": []
}
```

### Submit Invoke Blob

#### Authorization

Authorization must be sent with all client requests. The Bearer Access Token needed to authenticate and authorize user to access the API

**Request**

```bash
POST /ztx/contract/paymaster/submit
```

**CURL Command**

```bash
curl POST https://{endpoint}/ztx/contract/paymaster/submit
```

| Type | Params                                                                                                  | Values                                                                                       |
| ---- | ------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| HEAD | `Authorization`                                                                                         | `String`                                                                                     |
| POST | `blob`                                                                                                  | `String`                                                                                     |
| POST | `hash`                                                                                                  | `String`                                                                                     |
| POST | <p><code>listSigners[]</code></p><ul><li><code>signBlob</code></li><li><code>publicKey</code></li></ul> | <p><code>Array[]</code>  </p><p> <br><code>String]</code></p><p><code>String</code> <br></p> |
| POST | `initiator`                                                                                             | `String`                                                                                     |

**blob**

`blob` is transaction blob that need to signed

**hash**

`hash` is transaction hash

**signBlob**

`signBlob` is generated blob that have been signed by user wallet private key

**publicKey**

`publicKey` is user wallet public key

**initiator**

`initiator` is wallet address of paymaster resulted from /contract/generate-blob

#### Function&#x20;

To submit a blob to execute a transaction.

#### Request Body

The body is in json format

Here body transfer is the transaction data. For specific json format and parameters, see Transaction Structure. Example：

```json
{
     "blob": "0A255A5458334B5A6844567A7A3873363554486B726B4A714D547332354671414E356742446F31100822B401080712255A5458336274397A6F34525571463445675654396D65723537714841627871794B7A7A6B376288010A255A545833636A4C6455584B3941715157794C4654543732786D5778566873475156794255561A5F7B226D6574686F64223A227472616E73666572222C22706172616D73223A7B22746F223A225A545833615575384B62754D52394E3841544358746F59386B55386F657052624443686146222C2276616C7565223A2231303030303030227D7D309E693805",
        "hash": "b1bed4ca228f2cb36039f1d2c087051008e98d2267c2a4ca7e11ba4d6392db2d",
    "listSigner": [
        {
            "signBlob": "A7FAF9AE306D668ECC696EE098FF9D64C5701D67C0EA0A17D6CE95A54E926973F31DEFD9650AEC5FA85372A55840D2A38ED0AF6BE6AD45CBECB871B07B82E408",
            "publicKey": "b001c867ba4bc9378b8a7bd848b0713847720fcbbe0446737f2172a2d8248cc97934e66464c9"
        }
    ],
    "initiator": "ZTX3KZhDVzz8s65THkrkJqMTs25FqAN5gBDo1"
}

```

#### Response

```json
{
    "object": {
        "hash": "a4e42e4c0f8be1eb089bdc0d9a9a6fe0e465f3e1956771157889bcdd2f767452",
        "paymasterAddress": "ZTX3cpRnFgi24PMUvnqxCBb7Lc8rJZuqEcTnR",
        "message": "Success"
    },
    "messages": []
}
```
