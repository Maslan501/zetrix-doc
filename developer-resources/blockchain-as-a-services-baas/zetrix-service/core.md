---
description: >-
  Blockchain Node List API, Node Stat API, Node Count API, Block Height, Total
  Supply, Circulation Supply and Blockchain Information API.
---

# Core

Each of these APIs serves a specific purpose within the context of blockchain networks. They enable developers and users to gather insights, monitor network health, and access various data points related to nodes and the overall blockchain system.&#x20;

The **Node List API** provides detailed information about a specific node in a blockchain network. It allows you to retrieve various details related to a particular node, such as node address, node status, block height, network connectivity, and other relevant metadata. This API is particularly useful for monitoring and analyzing individual nodes within the network and assessing their performance and status.

The **Node Stat API** provides statistical information and metrics related to individual nodes in a blockchain network. It allows users to retrieve data about the performance, status, and behavior of specific nodes within the network.

The **Blockchain Information API** provides comprehensive data and information about the blockchain network as a whole. It includes various endpoints to access essential blockchain details, such as the current block height, transaction history, chain name, transaction list and other relevant information. This API is typically used to obtain an overview of the blockchain network and to retrieve historical and real-time data about the blockchain's operation.&#x20;

The **Node Count API** provides information about the number of active nodes in a blockchain network. It typically returns a count or statistical data indicating the total number of nodes currently participating in the network. This API helps monitor the health and decentralization of the blockchain network by tracking the number of active nodes and ensuring a robust network infrastructure.

The **Total Supply API** provides information about the total amount of tokens that have been created or issued on the blockchain. This includes tokens that are currently in circulation as well as tokens that are locked, reserved, or otherwise not actively circulating. This API is used to understand the maximum or current issuance of the blockchain’s native token.

The **Circulating Supply API** provides information about the number of tokens that are actively circulating in the market. It excludes tokens that are locked, burned, reserved, or held in non-circulating accounts. This API is commonly used for market analysis, token economics evaluation, and price-related metrics.

### Node list

```
GET HTTP /ztx/core/node-list?nodeType=VALIDATOR_NODE&pageNo=1&pageSize=100
```

CURL Command

```bash
curl GET https://{endpoint}/ztx/core/node-list?nodeType=VALIDATOR_NODE&pageNo=1&pageSize=100
```

* Function&#x20;

To view list of node running.

* Return Value

```json
{
    "object": {
        "pageNum": 1,
        "pageSize": 100,
        "content": [
            {
                "chainId": null,
                "nodeName": "Node 21",
                "nodeAddress": "ZTX3XY22vmRwWuKxn7UNuXdpLANfBx5Gx9bca",
                "consensusAddress": null,
                "monitored": null,
                "nodeType": null,
                "nodeStatus": "RUNNING",
                "operatingSystem": null,
                "expirationTime": null,
                "expirationTimeDay": null,
                "certEndTime": null,
                "certEndTimeDay": null,
                "licenseEndTime": null,
                "licenseEndTimeDay": null,
                "blockSeq": null,
                "blockHash": null
            },
            ........................
            {
                "chainId": null,
                "nodeName": "Node 1",
                "nodeAddress": "ZTX3RuTB1VcoNi7pmBUDFvAXe3HB6rKf7jeW2",
                "consensusAddress": null,
                "monitored": null,
                "nodeType": null,
                "nodeStatus": "RUNNING",
                "operatingSystem": null,
                "expirationTime": null,
                "expirationTimeDay": null,
                "certEndTime": null,
                "certEndTimeDay": null,
                "licenseEndTime": null,
                "licenseEndTimeDay": null,
                "blockSeq": null,
                "blockHash": null
            },

}
```

### Node stat

```
GET HTTP /ztx/core/node-stat
```

CURL Command

```bash
curl GET https://{endpoint}/ztx/core/node-stat
```

* Function&#x20;

To view statistics of node running.

* Return Value

```json
{
    "object": {
        "total": 38,
        "validatorNodeStat": {
            "total": 35,
            "onlineCount": 31,
            "offlineCount": 4,
            "warnCount": 0
        },
        "synchronousNodeStat": {
            "total": 3,
            "onlineCount": 0,
            "offlineCount": 1,
            "warnCount": 1
        }
    },
    "messages": []
}

```

### Blockchain info

```
GET HTTP /ztx/core/blockchain-info
```

CURL Command

```bash
curl GET https://{endpoint}/ztx/core/blockchain-info
```

* Function&#x20;

To view overall of blockchain information.

* Return Value

```json
{
    "object": {
        "chainId": 1635227727,
        "transactionNum": 1138594,
        "accountNum": 109614,
        "blockchainHeight": 1342741,
        "txTpsMax": 6,
        "currentTps": 0,
        "txNumLastDay": 90,
        "txNumPs": 0,
        "blockAverageGeneTime": 36,
        "preBlockGeneTime": 21,
        "netStatus": "NORMAL",
        "operationTime": 568,
        "updateTime": 1689041401239,
        "chainName": "ZETRIX Genesis Chain",
        "lastLedgerList": [
            {
                "seq": 1342741,
                "txNum": 0,
                "ledgerAge": 24
            },
            {
                "seq": 1342740,
                "txNum": 0,
                "ledgerAge": 84
            },
            {
                "seq": 1342739,
                "txNum": 0,
                "ledgerAge": 144
            },
            {
                "seq": 1342738,
                "txNum": 0,
                "ledgerAge": 204
            },
            {
                "seq": 1342737,
                "txNum": 0,
                "ledgerAge": 264
            }
        ],
        "lastTxList": [
            {
                "senderAddress": "ZTX3TMHqen5SSFhgDRPapLwR7MXn5UYuQBzsS",
                "txHash": "ac343453c7fe8b60773739582f363efbd1d2af60def3296dc7b96563cd4a2a01",
                "txTime": 1689040801000
            },
            {
                "senderAddress": "ZTX3Uz3b6CTXXDNQfv6W4Kuje98CDqqVH6wkB",
                "txHash": "83770bb477e5a0f37699e0c76cf93041201659320c81b2b654ba176b35bcfc10",
                "txTime": 1689040038000
            },
            {
                "senderAddress": "ZTX3TMHqen5SSFhgDRPapLwR7MXn5UYuQBzsS",
                "txHash": "7ee4f74edc949cb4dacdbfe65d635cdc339676d560f5b9efcef1fd7e8c5c44e5",
                "txTime": 1689037205000
            },
            {
                "senderAddress": "ZTX3K4ShVibYSf22yTu4SMH3ZUBn2KwiFG1jQ",
                "txHash": "9b9c995362d7dc4ea023d38b9c0fd2440937c9600860bc99fb42732a888a16c9",
                "txTime": 1689036655000
            },
            {
                "senderAddress": "ZTX3K4ShVibYSf22yTu4SMH3ZUBn2KwiFG1jQ",
                "txHash": "f4cf7bfc7a20a3498a6cd8b607fba1f23c786c1418e05561cbc4b305f0c75966",
                "txTime": 1689036628000
            }
        ]
    },
    "messages": []
}

```

### Node count

```
GET HTTP /ztx/core/node-count
```

CURL Command

```bash
curl GET https://{endpoint}/ztx/core/node-count
```

* Function&#x20;

To view node running based on geographical. Geography Code is refer as follow:

| geographyCode | Region        |
| ------------- | ------------- |
| 0             | Asia          |
| 1             | Europe        |
| 2             | Africa        |
| 3             | Oceania       |
| 4             | North America |
| 5             | South America |

* Return Value

```json
{
    "object": [
        {
            "geographyCode": 0,
            "total": 12
        },
        {
            "geographyCode": 1,
            "total": 6
        },
        {
            "geographyCode": 4,
            "total": 3
        },
        {
            "geographyCode": 5,
            "total": 2
        },
        {
            "geographyCode": 3,
            "total": 6
        },
        {
            "geographyCode": 2,
            "total": 6
        }
    ],
    "messages": []
}
```

### Block Height

```
GET HTTP /ztx/core/block-height
```

CURL Command

```bash
GET https://{endpoint}/ztx/core/block-height
```

* Function

To get latest block height from the node

* Return Value

```json
{
    "object": "1582107",
    "messages": []
}
```

### Total Supply

```
GET HTTP /ztx/core/total-supply
```

CURL Command

```bash
GET https://{endpoint}/ztx/core/total-supply
```

* Function

To get latest total supply from the node.

* Return Value.

```
1157075644.000000
```

### Circulation Supply

```
GET HTTP /ztx/core/circulation-supply
```

CURL Command

```bash
GET https://{endpoint}/ztx/core/circulation-supply
```

* Function

To get latest circulation supply from the node.

* Return Value.

```
273453899.844711
```
