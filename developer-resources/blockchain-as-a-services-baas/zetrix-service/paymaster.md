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

#### Root Parameters

| Field         | Type   | Required | Description                               |
| ------------- | ------ | -------- | ----------------------------------------- |
| userAddress   | string | ✅        | Wallet address initiating the transaction |
| operationType | string | ✅        | Logical operation category                |
| metadata      | object | ❌        | Custom metadata for tracking              |
| operations    | array  | ✅        | List of transaction operations            |

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
  "userAddress": "ZTX3QkbTjJsc7xDbwRtuHn826cAYF79uKR3rt",
  "operationType": "TEST_TRANSACTION",
  "metadata": {
    "id": "tx_pm_5"
  },
  "operations": [
    {
      "amount": "1.5",
      "inputStr": "",
      "fromAddress": "ZTX3QkbTjJsc7xDbwRtuHn826cAYF79uKR3rt",
      "type": "2",
      "toAddress": "ZTX3b69xfJZbiC8WbrtE2Ez27zri3a6K9B5pt",
      "precision": "",
      "payload": ""
    }
  ],
}'

```

#### Response

```json
{
    "object": {
        "blob": "0A255A5458334D4B46654B4A413342524165693747737A6D48526F79597259596F6B79554A4551100E2256080712255A545833516B62546A4A73633778446277527475486E383236634159463739754B52337274622B0A255A54583362363978664A5A62694338576272744532457A32377A72693361364B394235707410E0C65B30E45F3805",
        "hash": "054197b910b77a240f440ea5ca4c4dc0074023dc566fa749fc967efece92327f",
        "platformSignData": null,
        "actualFee": null,
        "blobId": "BLOB-3F1B883A021D4424",
        "paymasterAddress": "ZTX3MKFeKJA3BRAei7GszmHRoyYrYYokyUJEQ",
        "paymasterNonce": 14,
        "paymasterPoolId": 2,
        "expiresAt": "2026-04-06T09:28:30.072942652",
        "timeoutMinutes": 10
    },
    "messages": [],
    "success": true,
    "timestamp": "2026-04-06 09:18:30",
    "traceId": "74851775-6903-400c-abf7-ec1d37a7b9fc"
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

| Type | Params                                                                                                  | Values                                                                                         |
| ---- | ------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------------------- |
| HEAD | `Authorization`                                                                                         | `String`                                                                                       |
| POST | `blobId`                                                                                                | `String`                                                                                       |
| POST | `blob`                                                                                                  | `String`                                                                                       |
| POST | <p><code>listSigners[]</code></p><ul><li><code>signBlob</code></li><li><code>publicKey</code></li></ul> | <p><code>Array[]</code> </p><ul><li><code>String</code></li><li><code>String</code> </li></ul> |
| POST | `paymasterAddress`                                                                                      | `String`                                                                                       |

**blobId**

`blobId` is Identifier returned from `generate-blob`

**blob**

`blob` is transaction blob that need to signed

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
    "blobId": "BLOB-FCC1642A329D4768",
    "blob": "0A255A545833475548766172366278637948637A6543796D6153334B535341356B75775556445510032256080712255A545833516B62546A4A73633778446277527475486E383236634159463739754B52337274622B0A255A54583362363978664A5A62694338576272744532457A32377A72693361364B394235707410E0C65B3080FA01380A",
    "listSigner": [
        {
            "signBlob": "BD966E7A15599505B018CB623391BA5C58ABEB1054CC3043C36A0F3D7993BBAA3B65244ED7B158BB7A2D63CC5A0F6B8D2F535D1FB758B8E5B1CF5541CA5CC204",
            "publicKey": "b001eff30af3427a38c5cd021f5ac28578d27c3bd1ab53fc4d2789c1f8cb1827e83c58de414a"
        }
    ]
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

| Type | Params                                                                                          | Values                                                                                       |
| ---- | ----------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------- |
| HEAD | `Authorization`                                                                                 | `String`                                                                                     |
| POST | `userAddress`                                                                                   | `String`                                                                                     |
| POST | `contractAddress`                                                                               | `String`                                                                                     |
| POST | `method`                                                                                        | `String`                                                                                     |
| POST | <p><code>inputParameters</code></p><ul><li><code>to</code></li><li><code>value</code></li></ul> | <p><code>Array[]</code></p><ul><li><code>String</code></li><li><code>String</code></li></ul> |

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
    "userAddress":"ZTX3QkbTjJsc7xDbwRtuHn826cAYF79uKR3rt",
    "contractAddress": "ZTX3ZfbJ8AT8u8W7dx3g5tqbJ7nnGoYCszaj6",
    "method": "transfer",
    "inputParameters": {
        "to": "ZTX3b69xfJZbiC8WbrtE2Ez27zri3a6K9B5pt",
        "value": "500000000"
    }
}
```

#### Response

```json
{
    "object": {
        "blob": "0A255A5458334D4B46654B4A413342524165693747737A6D48526F79597259596F6B79554A4551100F22B601080712255A545833516B62546A4A73633778446277527475486E383236634159463739754B52337274628A010A255A5458335A66624A384154387538573764783367357471624A376E6E476F5943737A616A361A617B226D6574686F64223A227472616E73666572222C22706172616D73223A7B22746F223A225A54583362363978664A5A62694338576272744532457A32377A72693361364B3942357074222C2276616C7565223A22353030303030303030227D7D30FCBD02380A",
        "hash": "97f8c9f0721cecc871848e099412aae325c4f1fb1288863ad855465007123ab4",
        "platformSignData": null,
        "blobId": "BLOB-FD7247F70FF84BF3",
        "paymasterAddress": "ZTX3MKFeKJA3BRAei7GszmHRoyYrYYokyUJEQ",
        "paymasterNonce": 15,
        "paymasterPoolId": 2,
        "expiresAt": "2026-04-06T09:42:42.448084207",
        "timeoutMinutes": 10,
        "contractAddress": "ZTX3ZfbJ8AT8u8W7dx3g5tqbJ7nnGoYCszaj6",
        "method": "transfer",
        "actualFee": 40700
    },
    "messages": [],
    "success": true,
    "timestamp": "2026-04-06 09:32:42",
    "traceId": "7cd09ac8-448f-49e9-87ff-82bf366ed62b"
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
| POST | `blobId`                                                                                                | `String`                                                                                     |
| POST | `blob`                                                                                                  | `String`                                                                                     |
| POST | <p><code>listSigners[]</code></p><ul><li><code>signBlob</code></li><li><code>publicKey</code></li></ul> | <p><code>Array[]</code>  </p><p> <br><code>String]</code></p><p><code>String</code> <br></p> |

**blobId**

`blobId` is Identifier returned from `generate-blob`

**blob**

`blob` is transaction blob that need to signed

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
    "blobId": "BLOB-3F6BCE4AA08D4D88",
    "blob": "0A255A5458335A3176633233554A6B576B38765139733754576B7070725846336A4255636A346F102F22B601080712255A545833516B62546A4A73633778446277527475486E383236634159463739754B52337274628A010A255A5458335A66624A384154387538573764783367357471624A376E6E476F5943737A616A361A617B226D6574686F64223A227472616E73666572222C22706172616D73223A7B22746F223A225A54583362363978664A5A62694338576272744532457A32377A72693361364B3942357074222C2276616C7565223A22353030303030303030227D7D30FCBD02380A",
    "listSigner": [
        {
            "signBlob": "1481CAE9D963C98C903AB02C26A74FF6AE3B5FF0351144933CC0E748E7182E2723A584228DCB663D0EECA3F45A70431C4DB772AE13ADD247D897D18A28237001",
            "publicKey": "b001eff30af3427a38c5cd021f5ac28578d27c3bd1ab53fc4d2789c1f8cb1827e83c58de414a"
        }
    ]
}'

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
