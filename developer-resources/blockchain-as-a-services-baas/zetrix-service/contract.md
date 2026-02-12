# Contract

A **Contract Blockchain API** allows you to interact with smart contracts on a Zetrix blockchain network programmatically. Smart contracts are agreements that execute automatically when certain conditions are met, and they are stored on the blockchain.

With a **Contract Blockchain API,** developers can automate the interaction with these smart contracts, allowing them to build decentralized applications (DApps) and more complex blockchain-based systems.

This API typically uses a set of methods to interact with smart contracts, including methods to write data to the blockchain, read data from the blockchain, and execute smart contract functions.

#### Query Contract

```
HTTP POST /ztx/contract/query-address
```

CURL Command

```bash
curl POST https://{endpoint}/ztx/contract/query-address
```

* Function&#x20;

To query all the contract based contract key.

* The body is in json format

Here body transfer is the transaction data. For specific json format and parameters, see Transaction Structure. Example：

```json
{
    "address": "",
    "method": "",
    "inputParameters": {
    }
}
```

* Return Value

```json
{
    "object": {
        "result": {
            "type": "string",
            "value": ""
        }
    },
    "messages": [],
    "success": true,
    "timestamp": "",
    "traceId": ""
}
```

#### Invoke Contract – History Filter

```
HTTP GET /ztx/contract/history/filter
```

CURL Command

```bash
curl GET 'https://{endpoint}/ztx/contract/history/filter
```

* Function

Retrieve contract transaction history with optional filtering and pagination.

* Return Value

```json
{
  "object": {
    "page": 0,
    "pageSize": 10,
    "totalPages": 2,
    "totalElements": 17,
    "content": [
      {
        "id": 1,
        "address": "string",
        "contractKey": "string",
        "txInitiator": "string",
        "method": "string",
        "inputParameters": "string",
        "txHash": "string",
        "status": "string",
        "createdAt": "string",
        "updatedAt": "string"
      }
    ]
  },
  "messages": [],
  "success": true,
  "timestamp": "yyyy-MM-dd HH:mm:ss",
  "traceId": "string"
}

```

#### Invoke Contract - Generate Blob

```
HTTP POST /ztx/contract/generate-blob
```

CURL Command

```bash
curl POST https://{endpoint}/ztx/contract/generate-blob
```

* Function&#x20;

Generate a blob.

* The body is in json format

Here body transfer is the transaction data. For specific json format and parameters, see Transaction Structure. Example：

```json
{
  "contractKey": "string",
  "inputParameters": {},
  "method": "string",
  "txInitiator": "string"
}
```

* Return Value

<pre class="language-json"><code class="lang-json">{
<strong>  "messages": [
</strong>    {
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
</code></pre>

#### Invoke Contract - Sign Blob

```
HTTP POST /ztx/contract/sign-blob
```

CURL Command

```bash
curl POST https://{endpoint}/ztx/contract/sign-blob
```

* Function&#x20;

To sign transaction blob.

* The body is in json format

Here body transfer is the transaction data. For specific json format and parameters, see Transaction Structure. Example：

```json
{
  "privateKey": "",
  "blob": ""
}
```

* Return Value

```json
{
    "object": [
        {
            "signBlob": "",
            "publicKey": ""
        }
    ],
    "messages": [],
    "success": true,
    "timestamp": "",
    "traceId": ""
}
```

#### Invoke Contract - Submit&#x20;

```
HTTP POST /ztx/contract/submit
```

CURL Command

```bash
curl POST https://{endpoint}/ztx/contract/submit
```

* Function&#x20;

To invoke data to contract.

* The body is in json format

Here body transfer is the transaction data. For specific json format and parameters, see Transaction Structure. Example：

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
