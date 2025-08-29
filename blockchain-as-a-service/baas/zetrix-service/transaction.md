# Transaction

A **Zetrix Transaction Blockchain API** allows developers to send and receive transactions, read data, and access smart contract functions on a blockchain programmatically.

To create a transaction, you typically need to provide the sender's address, the recipient's address, the amount of the cryptocurrency to send, and a transaction fee, which is paid to the miners who verify and process the transaction. Once the transaction is created, it is broadcast to the network and added to a pool of unconfirmed transactions.

The **Zetrix Transaction Blockchain API** allows developers to interact with these unconfirmed transactions, retrieve information about them, and monitor their status in real-time. You can also use the transaction API to inspect the details of a confirmed transaction, including the amount sent, the recipient, and the timestamp of the transaction.

#### Generate Blob

```
POST HTTP /ztx/tx/generate-blob
```

CURL Command

```bash
curl POST https://{endpoint}/ztx/tx/generate-blob
```

* Function&#x20;

To generate blob based on input account.

* The body is in json data.

Here body transfer is the transaction data. For specific json format and parameters, see Transaction Structure.

```json
{
  "operations": [
    {
      "amount": "string",
      "fromAddress": "string",
      "inputStr": "string",
      "payload": "string",
      "precision": "string",
      "toAddress": "string",
      "type": "string"
    }
  ],
  "txInitiator": "string"
}
```

"type" field can be depicted as follows:

```
1 - CREATE_ACCOUNT - Creating account
2 - TRANSFER - Transfer token
3 - CALL_CONTRACT - Query or invoking contract
4 - CREATE_CONTRACT - Creating new contract
5 - SET_METADATA - Set metadata
```

* Return Value

```json
{
  "messages": [
    {
      "errorCode": 0,
      "message": "string",
      "type": "INFO"
    }
  ],
  "object": {
    "blob": "string",
    "hash": "string",
    "platformSignData": {
      "publicKey": "string",
      "signBlob": "string"
    }
  }
}
```

For example, to transfer Zetrix coin, developer can provide the following info in the payload where type is equals to 2.

```json
{
    "txInitiator": "ZTX3ZAikPMsNRCFoituy9TpYULvzKwz6NDRQi",
    "operations": [
        {
            "amount": "1",
            "inputStr": "",
            "fromAddress": "ZTX3ZAikPMsNRCFoituy9TpYULvzKwz6NDRQi",
            "type": "2",
            "toAddress": "ZTX3Lsfv96VEB98tV771wwgrUPT8sNNq8Wf9A",
            "precision": "",
            "payload": ""
        }
    ]
}
```

Response from the example :&#x20;

```json
{
    "object": {
        "blob": "0A255A5458335A41696B504D734E5243466F6974757939547059554C767A4B777A364E4452516910022256080712255A5458335A41696B504D734E5243466F6974757939547059554C767A4B777A364E44525169622B0A255A5458334C7366763936564542393874563737317777677255505438734E4E71385766394110C0843D30AE71380A",
        "hash": "8a4adfda356da062c608d33cbd8db40c703dc123c4f718e4b6444a152fc50e3f",
        "platformSignData": null
    },
    "messages": []
}
```

#### Parse Blob

```
POST HTTP /ztx/tx/parse-blob
```

CURL Command

```bash
curl POST https://{endpoint}/ztx/tx/parse-blob
```

* Function&#x20;

To parse a blob.

* The body is in json data.

Here body transfer is the transaction data. For specific json format and parameters, see Transaction Structure.

```json
{
  "blob": "string"
}
```

* Return Value

```json
{
  "messages": [
    {
      "errorCode": 0,
      "message": "string",
      "type": "INFO"
    }
  ],
  "object": {
    "chainId": 0,
    "feeLimit": 0,
    "gasPrice": 0,
    "nonce": 0,
    "operations": [
      {
        "createAccount": {
          "contract": {
            "owner": "string",
            "payload": "string",
            "type": 0
          },
          "destAddress": "string",
          "initBalance": 0,
          "initInput": "string",
          ....
          }
        },
        "issueAsset": {
          "amount": 0,
          "code": "string"
        },
        ....
      }
    ],
    "sourceAddress": "string"
  }
}
```

#### Query Transaction By Hash

```
GET HTTP /ztx/tx/query
```

CURL Command

```bash
curl GET https://{endpoint}/ztx/tx/query?hash=<TX_HASH>
```

* Function&#x20;

To query a transaction.

* The body is in json data.

Here body transfer is the transaction data. For specific json format and parameters, see Transaction Structure.

| Name | Description      | Format |
| ---- | ---------------- | ------ |
| hash | transaction hash | string |

```json
hash
```

* Return Value

```json
{
  "messages": [
    {
      "errorCode": 0,
      "message": "string",
      "type": "INFO"
    }
  ],
  "object": {
    "txContent": {
      "actualFee": "string",
      "closeTime": 0,
      "contractTxHashes": [
        "string"
      ],
      "errorCode": 0,
      "errorDesc": "string",
      "hash": "string",
      "ledgerSeq": 0,
      "signatures": [
        {
          "publicKey": "string",
          "signData": "string"
        }
      ],
      "transaction": {
        "chainId": 0,
        "feeLimit": 0,
        "gasPrice": 0,
        "metadata": "string",
        "nonce": 0,
        ...
        "sourceAddress": "string"
      },
      "trigger": {
        "transaction": {
          "hash": "string"
        }
      },
      "txSize": 0
    },
    "txStatus": "string"
  }
}
```

#### Sign Blob

```
POST HTTP /ztx/tx/sign-blob
```

CURL Command

```bash
curl POST https://{endpoint}/ztx/tx/sign-blob
```

* Function&#x20;

To sign transaction blob.

* The body is in json data.

Here body transfer is the transaction data. For specific json format and parameters, see Transaction Structure.

```json
{
  "blob": "string",
  "hash": "string",
  "privateKeys": [
    "string"
  ]
}
```

* Return Value

```json
{
  "messages": [
    {
      "errorCode": 0,
      "message": "string",
      "type": "INFO"
    }
  ],
  "object": [
    {
      "publicKey": "string",
      "signBlob": "string"
    }
  ]
}
```

#### Submit Transaction

```
// POST HTTP /ztx/tx/submit
```

CURL Command

```bash
// curl POST https://{endpoint}/ztx/tx/submit
```

* Function&#x20;

To submit transaction.

* The body is in json data.

Here body transfer is the transaction data. For specific json format and parameters, see Transaction Structure.

```json
{
  "blob": "string",
  "hash": "string",
  "initiator": "string",
  "listSigner": [
    {
      "publicKey": "string",
      "signBlob": "string"
    }
  ]
}
```

* Return Value

```json
{
  "messages": [
    {
      "errorCode": 0,
      "message": "string",
      "type": "INFO"
    }
  ],
  "object": {
    "hash": "string"
  }
}
```
