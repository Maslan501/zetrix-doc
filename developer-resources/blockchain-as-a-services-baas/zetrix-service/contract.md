# Contract

A **Contract Blockchain API** allows you to interact with smart contracts on a Zetrix blockchain network programmatically. Smart contracts are agreements that execute automatically when certain conditions are met, and they are stored on the blockchain.

With a **Contract Blockchain API,** developers can automate the interaction with these smart contracts, allowing them to build decentralized applications (DApps) and more complex blockchain-based systems.

This API typically uses a set of methods to interact with smart contracts, including methods to write data to the blockchain, read data from the blockchain, and execute smart contract functions.

#### Query Contract

```
HTTP POST /ztx/contract/query
```

CURL Command

```bash
curl POST https://{endpoint}/ztx/contract/query
```

* Function&#x20;

To query all the contract based contract key.

* The body is in json format

Here body transfer is the transaction data. For specific json format and parameters, see Transaction Structure. Example：

```json
{
  "contractKey": "string",
  "inputParameters": {},
  "method": "string"
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
    "result": {
      "type": "string",
      "value": "string"
    }
  }
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
