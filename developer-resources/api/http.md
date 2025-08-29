# HTTP

### Give it a go!

If your blockchain has just been deployed, it will initially contain only the **genesis account**. You can verify this account using the HTTP interface. For example, on a local setup on port **36002**, use the following endpoint:

```http
http://127.0.0.1:36002/v1/account/getGenesisAccount
```

This will return details of the genesis account, including address, balance, and other initial parameters.

```http
HTTP GET https://{endpoint}/getGenesisAccount
or
curl get https://{endpoint}/getGenesisAccount
```

If you would like to use our testnet or mainnet as reference, the respective endpoints are below:

Testnet: [https://test-node.zetrix.com](https://test-node.zetrix.com)

Mainnet: [https://node.zetrix.com](https://node.zetrix.com)



The response content should be as follows:

```json
{
  "error_code" : 0,
  "result" : {
    "address" : "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV",
    "assets" : null,
    "balance" : 100000000000000000,
    "metadatas" : null,
    "priv" : {
      "master_weight" : 1,
      "thresholds" : {
        "tx_threshold" : 1
      }
    }
  }
}
```

The value of `address` in the returned result is the genesis account address.

### HTTP Interfaces

#### Generating Public and Private Key pairs - Test

```http
HTTP GET /createKeyPair
```

*   CURL command

    ```shell
    curl get https://{endpoint}/createKeyPair
    ```
*   Function

    > **Note:** This interface is intended **only for testing purposes** and should **not be used in a production environment**. In production, use the SDK or command line tools. Calling this interface on an untrusted or malicious node server may expose your **private key**, as it only generates a key pair and does **not broadcast anything to the blockchain**. Use it with caution and only in isolated, secure testing environments.
*   Return value

    ```json
    {
      "error_code" : 0,
      "result" : {
        "address" : "ztxSkMvC2RcvXLpDgXBJCSb4m9mPe8gpNDKAG",  //The account address
        "private_key" : "privbzMRMFKoT8xPSPLqXoJb3GxgoULcR2go9WVNPG2N9e9oX251AgAU",  //The private key of the account
        "private_key_aes" : "7594a97bc5e6432704cc5f58ff60727ee9bda10a6117915d025553afec7f81527cb857b882b7c775391fe1fe3f7f3ec198ea69ada138b19cbe169a1a3fa2dec8",  //The data after the private key of the account is encrypted with AES
        "public_key" : "b00101da11713eaad86ad8ededfc28e86b8cd619ca2d593a21d8b82da34320a7e63b09c279bc", //The public key of the account
        "public_key_raw" : "01da11713eaad86ad8ededfc28e86b8cd619ca2d593a21d8b82da34320a7e63b",  //The data of the public key excluding the prefix and suffix
        "sign_type" : "ed25519"  //The type of the account encryption
      }
    }
    ```

#### getAccount

```http
HTTP GET /getAccount?address=ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV&key=hello&code=xxx&issuer=xxx
```

* CURL command

```shell
curl get https://{endpoint}/getGenesisAccount?address=ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV
```

*   Function

    Return information about the specified account and all its assets and metadata.
*   Parameters

    | Parameter                    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                           |
    | ---------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
    | address (required)           | Zetrix account address.                                                                                                                                                                                                                                                                                                                                                                                                                               |
    | key (optional)               | Specifies the key to retrieve a specific metadata value associated with the account. If left blank, the response will return **all metadata** linked to the account.                                                                                                                                                                                                                                                                                  |
    | code, issuer (both optional) | <p><strong>Asset Code and Asset Issuer:</strong> These two fields must be either <strong>both be provided</strong> or <strong>both left blank</strong>.</p><ul><li>If <strong>left blank</strong>, the response will include <strong>all assets</strong> associated with the account.</li><li>If <strong>provided</strong>, the response will return details <strong>only for the specified asset</strong> (matching both code and issuer).</li></ul> |
*   Return value

    The content returned is as follows:

    ```json
    {
    "error_code" : 0,//Error code, 0 means success
    "result" : {
      "address" : "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV", //The address of the account
      "balance" : 300000000000,//Gas balance, in UGas
      "nonce" : 1, //The number of transactions that the account has currently executed. If nonce is 0, this field is not displayed.
      "assets" : [//The asset list
        {
          "amount" : 1400,//The amount of the asset
          "key"　://Keyword of this asset
          {
            "code" : "CNY",//The asset code
            "issuer" : "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV"　//The account address of the asset issuer 
          }
        }, {
          "amount" : 1000,
          "key" :
          {
            "code" : "USD",
            "issuer" : "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV"
          }
        }
      ],
      "assets_hash" : "9696b03e4c3169380882e0217a986717adfc5877b495068152e6aa25370ecf4a",//The hash value generated by the asset list
      "contract" : null,//Contract. **Empty** means that the current contract is not a contract
      "metadatas" : [//Metadata list
        {
          "key" : "123",//Keyword of metadata
          "value" : "123_value",//Metadata content
          "version" : 1 // Metadata version
        }, {
          "key" : "456",
          "value" : "456_value",
          "version" : 1
        }, {
          "key" : "abcd",
          "value" : "abcd_value",
          "version" : 1
        }
      ],
      "metadatas_hash" : "82c8407cc7cd77897be3100c47ed9d43ec4097ee1c00e2c13447187e5b1ac66c",//The hash value generated by the metadata list
      "priv" : {//The privilege of the account
        "master_weight" : 1,//The weight for the account
        "thresholds" : {
          "tx_threshold" : 1//The weight required to initiate a transaction
        }
      }
    }
    }
    ```

    \
    If the account does not exist, the content returned is as follows:

    ```json
    {
    "error_code" : 4,//Error code, 4 means the account does not exist
    "result" : null
    }
    ```

#### getAccountBase

```http
HTTP GET /getAccountBase?address=ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV
```

*   CURL command

    ```shell
    curl get https://{endpoint}//getAccountBase?address=ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV
    ```
*   Function

    Return basic information about the specified account, excluding assets and metadata.
*   Parameter

    | Parameter          | Description             |
    | ------------------ | ----------------------- |
    | address (required) | Zetrix account address. |
*   Return value

    The content returned is as follows:

    ```json
    {
    "error_code" : 0,//Error code, 0 indicates success
    "result" : {
      "address" : "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV", //The address of the account
      "assets_hash" : "9696b03e4c3169380882e0217a986717adfc5877b495068152e6aa25370ecf4a",//The hash value generated by the asset list
      "balance" : 899671600,//Gas balance in UGas
      "contract" : null,//Contract. **Empty** indicates the current account is not a contract account
      "nonce" : 1, //The number of transactions that the account has currently executed. If nonce is 0, this field is not displayed.
      "priv" : {//Account privileges
        "master_weight" : 1,//The weight of the account
        "thresholds" : {
          "tx_threshold" : 1 //The weight required to initiate a transaction
        }
      },
      "metadatas_hash" : "82c8407cc7cd77897be3100c47ed9d43ec4097ee1c00e2c13447187e5b1ac66c"　// The hash value generated by the metadata list
    }
    }
    ```

    \
    If the account does not exist, the content returned is as follows:

    ```json
    {
    "error_code" : 4,//Error code, 4 indicates the account does not exist
    "result" : null
    }
    ```

#### getAccountAssets

```http
HTTP GET /getAccountAssets?address=ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV
```

*   CURL command

    ```shell
    curl get https://{endpoint}/getAccountAssets?address=ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV
    ```
*   Function

    Return the asset information of the specified account.
*   Parameters

    | Parameter                    | Description                                                                                                                                                                                                                                                                                                                                                                                                                                           |
    | ---------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
    | address (required)           | Zetrix account address.                                                                                                                                                                                                                                                                                                                                                                                                                               |
    | code, issuer (both optional) | <p><strong>Asset Code and Asset Issuer:</strong> These two fields must be either <strong>both be provided</strong> or <strong>both left blank</strong>.</p><ul><li>If <strong>left blank</strong>, the response will include <strong>all assets</strong> associated with the account.</li><li>If <strong>provided</strong>, the response will return details <strong>only for the specified asset</strong> (matching both code and issuer).</li></ul> |
*   Return value

    The content returned is as follows:

    ```json
    {
    "error_code" : 0,//Error code, 0 indicates the account does not exist
    "result": [//If the result is not null, it indicates the asset is existed
      {
        "amount" : 1400,//The amount of assets owned
        "key" ://Asset identification, including asset code and issuer
        {
          "code" : "EES",//Asset code
          "issuer" : "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV" //The account address of the issuer
        }
      },
      {
        "amount" : 1000,
        "key" :
        {
          "code" : "OES",
          "issuer" : "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV"
        }
      }
    ]
    }
    ```

    \
    If the account does not hold any assets, the returned content is typically:

    ```json
    {
    "error_code" : 0,//Error code, 0 indicates the account does not existed
    "result" : null　//Result is null, indicating that the asset does not exist
    }
    ```

#### getAccountMetaData

```http
HTTP GET /getAccountMetaData?address=ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV
```

*   CURL command

    ```shell
    curl get https://{endpoint}/getAccountMetaData?address=ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV
    ```
*   Function

    Return the metadata information of the specified account.
*   Parameters

    | Parameter          | Description                           |
    | ------------------ | ------------------------------------- |
    | address (required) | Zetrix account address.               |
    | key (optional)     | Specify the key value in the metadata |
*   Return value

    The content returned is as follows:

    ```json
    {
    "error_code" : 0,//Error code, 0 indicates the account does not exist
    "result": {//Result is not null, indicating that metadata exists
      "123": {
        "key" : "123",
        "value" : "123_value",
        "version" : 1
      },
      "456": {
        "key" : "456",
        "value" : "456_value",
        "version" : 1
      },
      "abcd": {
        "key" : "abcd",
        "value" : "abcd_value",
        "version" : 1
      }
    }
    }
    ```

    \
    If the account does not have metadata, return the content:

    ```json
    {
    "error_code" : 0,//Error code, 0 indicates the account does not exist
    "result" : null //Result is null, indicating that the metadata does not exist
    }
    ```

#### getTransactionHistory

```http
HTTP GET /getTransactionHistory?ledger_seq=6
```

*   CURL command

    ```shell
    curl get https://{endpoint}/getTransactionHistory?ledger_seq=6
    ```
*   Function

    Return the completed transaction history.
*   Parameters

    | Parameter   | Description                                                       |
    | ----------- | ----------------------------------------------------------------- |
    | hash        | Query using the transaction hash, which is its unique identifier. |
    | ledger\_seq | Query all transactions in the specified block                     |

    > **Note**: The constraint between the two parameters operates as a **logical AND**. If both are specified, the system will query the **specific transaction within the specified block**.
*   Return value

    The content returned is as follows:

    > **Note**: Below are two transactions; the second one creates a contract account. Carefully review the `error_desc` field for any execution errors or issues during processing.

    ```json
    {
        "error_code": 0,//Error code, o indicates the transaction is exited
        "result": {
            "total_count": 2,//The number of transactions queried
            "transactions": [{//The transaction list
                "actual_fee": 313000,//The actual fee for the transaction
                "close_time": 1524031260097214,//When the transaction is completed
                "error_code": 0,// The error code of the transaction, 0 means the transaction is executed successfully, and non-zero means the transaction execution failed
                "error_desc": "",//Description for the error in the transaction
                "hash": "89a9d6e5d2c0e2b5c4fe58045ab2236d12e9449ef232342a48a2e2628e12014d",//The hash value of the transaction
                "ledger_seq": 6,//The block height of the transaction
                "signatures": [{//The signature list
                    "public_key": "b00180c2007082d1e2519a0f2d08fd65ba607fe3b8be646192a2f18a5fa0bee8f7a810d011ed",//Public key
                    "sign_data": "27866d70a58fc527b1ff1b4a693b8034b0078fc7ac7591fb05679abe5ca660db5c372922bfa8f26e76511e2c33386306ded7593874a6aec5baeeaddbd2012f06"//Data signed
                }],
                "transaction": {//Transaction content
                    "fee_limit": 10000000000,//The maximum fee provided for this transaction, in UGas
                    "gas_price": 1000,//The price of gas, in UGas
                    "nonce": 1,//The sequence number of the transaction in the account
                    "operations": [{//The operation list
                        "create_account": {//The operation to create an account
                            "dest_address": "ztxSrDYeqnCeyDFNJQDdMJok3PB1jAhmctaWY",//The address of the target account
                            "init_balance": 10000000,//The initial Gas of the target account, in UGas
                            "priv": {//The privilege of the target account
                                "master_weight": 1,//The weight of the target account
                                "thresholds": {
                                    "tx_threshold": 1 //The weight required to initiate a transaction for the target account
                                }
                            }
                        },
                        "type": 1 //The type of the operation, 1 means the operation is to create an account
                    },
                    {
                        "create_account": {
                            "dest_address": "ztxSVVQUx2GJSb1sfoWDMmHNpms4fuuA5s2WW",
                            "init_balance": 10000000,
                            "priv": {
                                "master_weight": 1,
                                "thresholds": {
                                    "tx_threshold": 1
                                }
                            }
                        },
                        "type": 1
                    }],
                    "source_address": "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV" //The account address to initiate the transaction
                },
                "tx_size": 313 //The transaction byte
            },
            {
                "actual_fee": 1000402000,//The actual fee for the transaction
                "close_time": 1524031260097214,//When the transaction is completed
                "error_code": 0,// The error code of the transaction, 0 means the transaction is executed successfully, and non-zero means the transaction execution failed
                "error_desc": "[{\"contract_address\":\"ztxSpuMcbgPuNALPdRMJEQZhrMfA5u9ycUZCo\",\"operation_index\":0}]", //The result of contract creation, including contract address and operation index values
                "hash": "4cbf50e03645f1075d7e5c450ced93e26e3153cf7b88ea8003b2fda39e618e64",//The hash value of the transaction
                "ledger_seq": 6,//The block height of the transaction
                "signatures": [{//The signature list
                    "public_key": "b00180c2007082d1e2519a0f2d08fd65ba607fe3b8be646192a2f18a5fa0bee8f7a810d011ed",//Public key
                    "sign_data": "87fdcad0d706479e1a3f75fac2238763cd15fd93f81f1b8889fb798cefbe1752c192bbd3b5da6ebdb31ae47d8b62bb1166dcceca8d96020708f3ac5434838604" //Data signed
                }],
                "transaction": {//Transaction content
                    "fee_limit": 20004420000,//The maximum fee for this transaction
                    "gas_price": 1000,//The price of gas
                    "nonce": 30,//The sequence number of the transaction in the account
                    "operations": [{//The operation list
                        "create_account": {//The operation to create an account
                            "contract": {//Contract
                                "payload": "'use strict';\n\t\t\t\t\tfunction init(bar)\n\t\t\t\t\t{\n\t\t\t\t\t  return;\n\t\t\t\t\t}\n\t\t\t\t\t\n\t\t\t\t\tfunction main(input)\n\t\t\t\t\t{\n\t\t\t\t\t  return;\n\t\t\t\t\t}\n\t\t     function query()\n\t\t\t\t\t{\n\t\t\t\t\t  return;\n\t\t\t\t\t}\n\t\t      \n\t\t          "　//Contract code
                            },
                            "init_balance": 10000000,//The initial Gas of the contract account, in UGas
                            "priv": {//The privilege of the contract account
                                "thresholds": {
                                    "tx_threshold": 1 //The weight required to initiate a contract account transaction
                                }
                            }
                        },
                        "type": 1 // The operation type, 1 indicates the operation to create an account
                    }],
                    "source_address": "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV" //The account address to initiate the transaction
                },
                "tx_size": 402 //The transaction byte
            }]
        }
    }
    ```

    \
    If no transaction is found, the response will be:

    ```json
    {
    "error_code": 4,//Error code, 4 indicates no transaction
    "result":
    {
      "total_count": 0,//The number of transactions queried
      "transactions": []
    }
    }
    ```

#### getTransactionCache

```http
HTTP GET /getTransactionCache?hash=ad545bfc26c440e324076fbbe1d8affbd8a2277858dc35927d425d0fe644e698&limit=100
```

*   CURL command

    ```shell
    curl get https://{endpoint}/getTransactionCache?hash=ad545bfc26c440e324076fbbe1d8affbd8a2277858dc35927d425d0fe644e698&limit=100
    ```
*   Function

    If a transaction has been submitted successfully but not yet executed, the response will include the transaction details with a status indicating it is **pending** or **not yet executed**, and `error_code` will typically be `0`, with execution fields left unset
*   Parameters

    <table><thead><tr><th width="360">Parameter</th><th>Description</th></tr></thead><tbody><tr><td>hash</td><td>Use the transaction hash, which is its unique ID, to query specific transaction details.</td></tr><tr><td>limit</td><td>Query multiple (N) transactions currently in the transaction queue to view their pending processing status.</td></tr></tbody></table>

    > **Note**: The constraint between the two parameters functions as a **logical OR**. If both parameters are provided, the system will prioritize and perform a **hash-based query**.
*   Return value

    The response will returned is as follows:

    ```json
    {
    "error_code": 0,//Error code, 0 indicates the query is successful
    "result": {
      "total_count": 1,//The total transactions
      "transactions": [//The transaction list
        {
          "hash": "a336c8f4b49c8b2c5a6c68543368ed3b450b6138a9f878892cf982ffb6fe234e",//The transaction hash
          "incoming_time": 1521013029435154,//When the transaction enters the cache queue
          "signatures": [//The signature list
            {
              "public_key": "b001882b9d1b5e7019f163d001c85194cface61e294483710f5e66ef40a4d387f5fcb0166f4f",//Public key
              "sign_data": "c5885144ffccb0b434b494271258e846c30a4551036e483822ee2b57400576e9e700e8960eb424764d033a2e73af6e6a2bfa5da390f71161732e13beee206107" //Data signed
            }
          ],
          "status": "processing",//The transaction status
          "transaction": {//Transaction content
            "fee_limit": 100000,//The maximum fee provided for this transaction, in UGas
            "gas_price": 1000,//The price of gas, in UGas
            "nonce": 2,//The sequence number of the transaction in the account
            "operations": [//The operation list
              {
                "create_account": {//The operation to create an account
                  "dest_address": "ztxSnVDvg3gfW8as4BeodEJDDQAMXu9NobyvN",//The address of the target account
                  "init_balance": 300000,//The initial Gas of the target account, in UGas
                  "priv": {//The privilege of the target account
                    "master_weight": 1,//The weight of the target account
                    "thresholds": {
                      "tx_threshold": 2 //The weight required to initiate a transaction for the target account
                    }
                  }
                },
                "type": 1　// The operation type, 1 indicates the operation to create an account
              }
            ],
            "source_address": "ztxSkScnQJ1ciM1C9rBVw6DbtogbAcWFuXRhC"// The account address to initiate the transaction
          }
        }
      ]
    }
    }
    ```

    \
    If no transaction is found, the response will be:

    ```json
    {
    "error_code": 4,//Error code, 4 indicates no transaction is queried
    "result":
    {
      "total_count": 0,//The number of transactions queried
      "transactions": []
    }
    }
    ```

#### getLedger

```http
HTTP GET /getLedger?seq=xxxx&with_validator=true&with_consvalue=true&with_fee=true
```

*   CURL command

    ```shell
    curl get https://{endpoint}/getLedger?seq=6&with_validator=true&with_consvalue=true&with_fee=true
    ```
*   Function

    Return information about block header.
*   Parameters

    <table><thead><tr><th width="170">Parameter</th><th>Description</th></tr></thead><tbody><tr><td>seq</td><td>The ledger serial number specifies a particular block; if left blank, the latest (current) ledger is returned.</td></tr><tr><td>with_validator</td><td>By default, this is set to <strong>false</strong>, so the list of verification nodes will <strong>not</strong> be displayed.</td></tr><tr><td>with_consvalue</td><td>By default, this is set to <strong>false</strong>, and the consensus value is <strong>not</strong> displayed.</td></tr><tr><td>with_fee</td><td>By default, this is set to <strong>false</strong>, so the cost configuration is not shown.</td></tr></tbody></table>
*   Return value

    Return response as follows:

    ```json
    {
    "error_code" : 0,//Error code, 0 indicates success
    "result" : {
      "block_reward" : 800000000,//Block reward, in UGas
      "consensus_value" : {//Consensus content
        "close_time" : 1524031260097214,//When the consensus is finished
        "ledger_seq" : 6,//The block height
        "previous_ledger_hash" : "ef329c7ed761e3065ab08f9e7672fd5f4e3ddd77b0be35598979aff8c21ada9b",//The hash of the previous block
        "previous_proof" : "0ac1010a2c080110022a26080310052220432dde2fd32a2a66da77647231821c87958f56c303bd08003633952d384eb0b61290010a4c623030316435363833363735303137666662633332366538666232303738653532316566383435373234363236353339356536383934633835323434656566643262666130386635393862661240deeb9b782410f0f86d897006cac8ad152e56e4f914e5d718706de84044ef98baef25512a337865772641d57090b5c77e9e2149dbd41910e8d6cd85c3387ea708",//The certificate of the previous block 
        "previous_proof_plain" : {//The content of the previous block certificate
          "commits" : [
            {
              "pbft" : {
                "commit" : {
                  "sequence" : 5,//The serial number of the block
                  "value_digest" : "432dde2fd32a2a66da77647231821c87958f56c303bd08003633952d384eb0b6",//Summary
                  "view_number" : 3 //The serial number of the view
                },
                "round_number" : 1,
                "type" : 2 //Type
              },
              "signature" : {//The signature of the node
                "public_key" : "b001d5683675017ffbc326e8fb2078e521ef8457246265395e6894c85244eefd2bfa08f598bf",//Public key
                "sign_data" : "deeb9b782410f0f86d897006cac8ad152e56e4f914e5d718706de84044ef98baef25512a337865772641d57090b5c77e9e2149dbd41910e8d6cd85c3387ea708"　//Data signed
              }
            }
          ]
        },
        "txset" : {//Transaction set
          "txs" : [//The transaction list
            {
              "signatures" : [//The signature list
                {
                  "public_key" : "b00180c2007082d1e2519a0f2d08fd65ba607fe3b8be646192a2f18a5fa0bee8f7a810d011ed",//Public key
                  "sign_data" : "27866d70a58fc527b1ff1b4a693b8034b0078fc7ac7591fb05679abe5ca660db5c372922bfa8f26e76511e2c33386306ded7593874a6aec5baeeaddbd2012f06" //Data signed
                }
              ],
              "transaction" : {//Transaction content
                "fee_limit" : 10000000000,//The maximum fee provided for this transaction, in UGas
                "gas_price" : 1000,//The price of gas, in UGas
                "nonce" : 1,//The sequence number of the transaction in the account
                "operations" : [//The operation list
                  {
                    "create_account" : {//The operation to create an account
                      "dest_address" : "ztxSrDYeqnCeyDFNJQDdMJok3PB1jAhmctaWY",//The target account
                      "init_balance" : 10000000,//The initial Gas of the target account, in UGas
                      "priv" : {//The privilege of the target account
                        "master_weight" : 1,//The weight owned by the target account
                        "thresholds" : {
                          "tx_threshold" : 1 //The weight required to initiate a transaction for the target account
                        }
                      }
                    },
                    "type" : 1 //The operation type, 1 indicates the operation is to create an account
                  },
                  {
                    "create_account" : {
                      "dest_address" : "ztxSVVQUx2GJSb1sfoWDMmHNpms4fuuA5s2WW",
                      "init_balance" : 10000000,
                      "priv" : {
                        "master_weight" : 1,
                        "thresholds" : {
                          "tx_threshold" : 1
                        }
                      }
                    },
                    "type" : 1
                  }
                ],
                "source_address" : "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV"
              }
            },
            {
              "signatures" : [
                {
                  "public_key" : "b00180c2007082d1e2519a0f2d08fd65ba607fe3b8be646192a2f18a5fa0bee8f7a810d011ed",
                  "sign_data" : "fb7d9d87f4c9140b6e19a199091c6871e2380ad8e8a8fcada9b42a2911057111dc796d731f3f887e600aa89cc8692300f980723298a93b91db711155670d3e0d"
                }
              ],
              "transaction" : {
                "fee_limit" : 10000000000,
                "gas_price" : 1000,
                "nonce" : 2,
                "operations" : [
                  {
                    "create_account" : {
                      "dest_address" : "ztxSrbWSdMvysHtKQqpJzKAduQmDFHa1VHhRN",
                      "init_balance" : 10000000,
                      "priv" : {
                        "master_weight" : 1,
                        "thresholds" : {
                          "tx_threshold" : 1
                        }
                      }
                    },
                    "type" : 1
                  },
                  {
                    "create_account" : {
                      "dest_address" : "ztxSWwQmECKbyTTyx8RGVbaTapPqpHSPeskJy",
                      "init_balance" : 10000000,
                      "priv" : {
                        "master_weight" : 1,
                        "thresholds" : {
                          "tx_threshold" : 1
                        }
                      }
                    },
                    "type" : 1
                  }
                ],
                "source_address" : "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV"
              }
            }
          ]
        }
      },
      "fees" : {//The fee standard of blocks
        "base_reserve" : 10000000,//The minimum Gas in the account, in UGas
        "gas_price" : 1000 //The price of gas, in UGas
      },
      "header" : {//The block header
        "account_tree_hash" : "6aca37dfe83f213942b21d02618b989619cfd7c0e67a8a14b0f7599dd4010aad",//The hash value of the account tree
        "close_time" : 1524031260097214,//When the block is generated
        "consensus_value_hash" : "14a65d69f619395135da2ff98281d5707494801f12184a4318b9a76383e651a8",//The hash value of onsensus content
        "fees_hash" : "916daa78d264b3e2d9cff8aac84c943a834f49a62b7354d4fa228dab65515313",//The hash value of the fee standard
        "hash" : "2cf378b326ab0026625c8d036813aef89a0b383e75055b80cb7cc25a657a9c5d",//The hash value of the block
        "previous_hash" : "ef329c7ed761e3065ab08f9e7672fd5f4e3ddd77b0be35598979aff8c21ada9b",//The hash value of the previous block
        "seq" : 6,//The block height
        "tx_count" : 2,//The total transactions
        "validators_hash" : "d857aa40ecdb123415f893159321eb223e4dbc11863daef86f35565dd1633316",//The hash value of the validation node list
        "version" : 1000 //The block version
      },
      "validators" : [//The validation node list
          "ztxSVW6zpibJ3amyauqk6339jjAgaL5LU9yAt" //The address of the validation node
      ]
     }
    }
    ```

    \
    If no ledger is found, the following response is returned:

    ```json
    { "error_code": 4, "result": null }
    ```

#### multiQuery

```http
HTTP POST /multiQuery
```

*   CURL Command

    ```shell
    curl localhost:26002/multiQuery -X POST -d "{\"items\":[{\"url\":\"getAccount?address=ztxSgJCoz19wLJ4KbFjb3KDFs96xeW966iYgD\",\"method\":\"GET\",\"jsonData\":\"\"},{\"url\":\"hello\",\"method\":\"GET\",\"jsonData\":\"\"}]}"
    ```
*   Function

    Returns multiple results based on the provided query list.
*   Here, the request body should contain the **query list**. For example:

    ```json
    {
      "items":
      [
          {
          "url":"https://{endpoint}/?address=ztxSgJCoz19wLJ4KbFjb3KDFs96xeW966iYgD", // Query Command
          "method": "GET", // GET or POST query
          "jsonData":"" // If post query, here is the content of body
          },
          {
          "url":"hello", // Query Command
          "method": "GET", // GET or POST query
          "jsonData":"" // If post query, here is the content of body
          }
      ]
    }
    ```
*   Return

    ```json
    {
        "error_code": 0, // Query error code
        "results": [
            {
                "error_code": 0, // getAccount error code
                "result": { // getAccount result
                    "address": "ztxSgJCoz19wLJ4KbFjb3KDFs96xeW966iYgD",
                    "assets": null,
                    "assets_hash": "ad67d57ae19de8068dbcd47282146bd553fe9f684c57c8c114453863ee41abc3",
                    "balance": 99989879492755000,
                    "metadatas": null,
                    "metadatas_hash": "ad67d57ae19de8068dbcd47282146bd553fe9f684c57c8c114453863ee41abc3",
                    "nonce": 10,
                    "priv": {
                        "master_weight": 1,
                        "thresholds": {
                            "tx_threshold": 1
                        }
                    }
                }
            },
            { // hello result
                "zetrix_version": "4.0.0",
                "current_time": "2020-02-28 17:33:37.282237",
                "hash_type": 0,
                "ledger_version": "4000",
                "license_version": "1.0.0",
                "monitor_version": "4000",
                "network_id": 30000,
                "overlay_version": "2000",
                "websocket_address": 36003
            }
        ]
    }
    ```

#### getTransactionBlob

```http
HTTP POST /getTransactionBlob
```

*   CURL command

    ```shell
    curl https://{endpoint}/getTransactionBlob -X POST -d "{\"source_address\":\"adxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV\",\"nonce\":1,\"fee_limit\":100000,\"gas_price\":1000,\"operations\":[{\"type\":1,\"create_account\":{\"dest_address\":\"ztxSX1SypC33doY4a5MYYr5zBZUvB33kKHdAo\",\"metadatas\":[{\"key\":\"hello\",\"value\":\metadata\"}],\"priv\":{\"thresholds\":{\"tx_threshold\":1}},\"contract\":{\"payload\":\"function main(inputStr){\n}\"}}}]}"
    ```
*   Function

    Returns both the **transaction hash** and the **hexadecimal string** representing the serialized transaction.
*   Here, the request body field **`transfer`** contains the transaction data. For the specific JSON structure and parameter definitions, refer to the **Transaction Structure** section.

    ```json
    {
      "source_address": "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV",
      "nonce": 1,
      "fee_limit": 100000,
      "gas_price": 1000,
      "operations": [{
        "type": 1,
        "create_account": {
          "dest_address": "ztxSX1SypC33doY4a5MYYr5zBZUvB33kKHdAo",
          "metadatas": [{
            "key": "hello",
            "value": "metadata"
          }],
          "priv": {
            "thresholds": {
              "tx_threshold": 1
            }
          },
          "contract": {
            "payload": "function main(inputStr){\n}"
          }
        }
      }]
    }
    ```
*   Return value

    ```json
    {
      "error_code": 0,//The serialized transaction error code
      "error_desc": "",//Description for the error
      "result": {
        "hash": "7ea4b61b86c5e2248e1ec24a8c04568e6b17c7121fb86f648b42b73bd3bada5d",//The transaction hash
        "transaction_blob": "0a2462755173396e70614371396d4e465a473138717538385a636d5859716436627170545533100118a08d0620e8073aad01080122a8010a24627551747336446654354b6176745639344a675a79373548397069776d62374b6f555767123a123866756e6374696f6e206d61696e28696e707574537472297b0a202f2ae8bf99e698afe59088e7baa6e585a5e58fa3e587bde695b02a2f207d1a041a020801223e0a0568656c6c6f1235e8bf99e698afe5889be5bbbae8b4a6e58fb7e79a84e8bf87e7a88be4b8ade8aebee7bdaee79a84e4b880e4b8aa6d65746164617461" //The hexadecimal representation after serializing the transaction
      }
    }
    ```

#### &#x20;<a href="#submittransaction" id="submittransaction"></a>

#### submitTransaction

```http
HTTP POST /submitTransaction
```

*   CURL command

    ```shell
    curl https://{endpoint}/submitTransaction -X POST -d "{\"items\":[{\"transaction_blob\":\"0a2e61303032643833343562383964633334613537353734656234393736333566663132356133373939666537376236100122b90108012ab4010a2e61303032663836366337663431356537313934613932363131386363353565346365393939656232396231363461123a123866756e6374696f6e206d61696e28696e707574537472297b0a202f2ae8bf99e698afe59088e7baa6e585a5e58fa3e587bde695b02a2f207d1a06080a1a020807223e0a0568656c6c6f1235e8bf99e698afe5889be5bbbae8b4a6e58fb7e79a84e8bf87e7a88be4b8ade8aebee7bdaee79a84e4b880e4b8aa6d65746164617461\",\"signatures\":[{\"sign_data\":\"2f6612eaefbdadbe792201bb5d1e178aff118dfa0a640edb2a8ee91933efb97c4fb7f97be75195e529609a4de9b890b743124970d6bd7072b7029cfe7683ba2d\",\"public_key\":\"b00204e1c7dddc36d3153adcaa451b0ab525d3def48a0a10fdb492dc3a7263cfb88e80ee974ca4da0e1f322aa84ff9d11340c764ea756ad148e979c121619e9fe52e9054\"},{\"sign_data\":\"90C1CD2CD371F581EB8GasCDA295C390D62C19FE7F080FB981584FB5F0BAB3E293B613C827CB1B2E063E5783FFD7425E1DEC0E70F17C1227FBA5997A72865A30A\",\"public_key\":\"b00168eceea7900ddcb8f694161755f98590ba7944de3bfe339610fe0cacc10a18372dcbf71b\"}]}]}"
    ```
*   Function

    Send the **serialized transaction** along with the **signature list** to the blockchain for processing and execution.
*   Request example:

    ```json
    {
      "items" : [{//The transaction package list
        "transaction_blob" : "0a2e61303032643833343562383964633334613537353734656234393736333566663132356133373939666537376236100122b90108012ab4010a2e61303032663836366337663431356537313934613932363131386363353565346365393939656232396231363461123a123866756e6374696f6e206d61696e28696e707574537472297b0a202f2ae8bf99e698afe59088e7baa6e585a5e58fa3e587bde695b02a2f207d1a06080a1a020807223e0a0568656c6c6f1235e8bf99e698afe5889be5bbbae8b4a6e58fb7e79a84e8bf87e7a88be4b8ade8aebee7bdaee79a84e4b880e4b8aa6d65746164617461",//The hexadecimal representation after serializing the transaction
        "signatures" : [{//The first signature
          "sign_data" : "2f6612eaefbdadbe792201bb5d1e178aff118dfa0a640edb2a8ee91933efb97c4fb7f97be75195e529609a4de9b890b743124970d6bd7072b7029cfe7683ba2d",//Data signed
          "public_key" : "b00204e1c7dddc36d3153adcaa451b0ab525d3def48a0a10fdb492dc3a7263cfb88e80ee974ca4da0e1f322aa84ff9d11340c764ea756ad148e979c121619e9fe52e9054"//Public key
        }, {//The second signature
          "sign_data" : "90C1CD2CD371F581EB8GasCDA295C390D62C19FE7F080FB981584FB5F0BAB3E293B613C827CB1B2E063E5783FFD7425E1DEC0E70F17C1227FBA5997A72865A30A",//Data signed
          "public_key" : "b00168eceea7900ddcb8f694161755f98590ba7944de3bfe339610fe0cacc10a18372dcbf71b"//Public key
        }
                       ]
      }
                ]
    }
    ```
*   Keywords in JSON

    <table><thead><tr><th width="184">Parameter</th><th width="101.3333740234375">Type</th><th>Description</th></tr></thead><tbody><tr><td>transaction_blob</td><td>string</td><td>The serialized transaction is represented in a <strong>hexadecimal string format</strong>, which encodes the transaction's binary data for transmission and verification.</td></tr><tr><td>sign_data</td><td>string</td><td>The signed data should be in <strong>hexadecimal format</strong> and represents the signature generated from the <code>transaction_blob</code>.<br><strong>Note:</strong> Before signing, <strong>convert <code>transaction_blob</code> to a byte stream</strong>—do <strong>not</strong> sign the hexadecimal string directly.</td></tr><tr><td>public_key</td><td>string</td><td>The public key must be provided in <strong>hexadecimal format</strong>, corresponding to the private key used to sign the transaction.</td></tr></tbody></table>
*   Return value

    > **Note**: The transaction is submitted and executed successfully.

    ```json
    {
    "error_code": 0,//The result of the submission
    "error_desc": "",//Description for the error
    "result": {
      "hash": "8e97ab885685d68b8fa8c7682f77ce17a85f1b4f6c8438eda8ec955890919405",//The transaction hash
    }
    }
    ```

#### &#x20;<a href="#callcontract" id="callcontract"></a>

#### callContract

```http
HTTP POST /callContract
```

*   CURL command

    ```shell
    curl https://{endpoint}/callContract -X POST -d "{\"contract_address\":\"\",\"code\":\"\\\"use strict\\\";log(undefined);function query() { return 1; }\",\"input\":\"{}\",\"contract_balance\":\"100009000000\",\"fee_limit\":100000000000000000,\"gas_price\":1000,\"opt_type\":2,\"source_address\":\"\"}"
    ```
*   Function

    In Zetrix's smart contract module, a **sandbox environment** is provided for safe debugging. During debugging, **neither the blockchain state nor the contract state is modified**. Zetrix offers the `callContract` interface specifically for this purpose it allows you to test smart contracts either by referencing a deployed contract on the public chain or by passing local contract code as parameters. Since this interface does **not submit actual transactions**, **no transaction fee is required**.
*   Request example:

    ```json
    {
      "contract_address" : "",//Optional, the smart contract address
      "code" : "\"use strict\";log(undefined);function query() { return 1; }",//Optional, the smart contract code
      "input" : "{}",//Optional, pass parameters to the contract to be called
      "contract_balance" : "100009000000",//The initial Gas balance assigned to the contract
      "fee_limit" : 100000000000000000,//The transaction fee
      "gas_price": 1000,//Optional, the gas price
      "opt_type" : 2,//Optional, the operation type
      "source_address" : "" //Optional, the original address of the simulated contract call
    }
    ```
*   Keywords in JSON

    | Keyword           | Type   | Description                                                                                                                                                                                                           |
    | ----------------- | ------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
    | contract\_address | string | The smart contract address to be called must be provided; if it cannot be found in the database, an error will be returned. If left blank, the system will default to using the content provided in the `code` field. |
    | code              | string | The `code` field contains the contract code to be debugged. If `contract_address` is not provided, the system will use the `code` field. If both `contract_address` and `code` are empty, an error will be returned.  |
    | input             | string | Pass the parameters to the contract to be called                                                                                                                                                                      |
    | contract\_balance | string | The initial Gas balance assigned to the contract                                                                                                                                                                      |
    | fee\_limit        | int64  | The transaction fee                                                                                                                                                                                                   |
    | gas\_price        | int64  | The price of gas                                                                                                                                                                                                      |
    | opt\_type         | int    | 0: call the contract's read-write interface `init`, 1: call the contract's read-write interface `main`, 2: call the read-only interface `query`                                                                       |
    | source\_address   | string | Simulate the original address of the contract called                                                                                                                                                                  |
*   Return value

    ```json
    {
      "error_code" : 0,//The result of the call, 0 means success
      "error_desc" : "",//Description of error code
      "result" : {
        "logs" : {
          "0-ztxSWaq8TmFVyyHKXsYwb9FTaGEx77EWW2hne" : null　//Not used any more
        },
        "query_rets" : [
          {
            "result" : {
              "type" : "bool", //　Return the name of the variable
              "value" : false  // The value of the variable is false
            }
          }
        ],
        "stat" : {
          "apply_time" : 6315,//Execution time
          "memory_usage" : 886176,//Memory usage
          "stack_usage": 2564,//Stack usage
          "step" : 3 //Frequency of execution
        },
        "txs" : null　//Transaction set
      }
    }
    ```

#### &#x20;<a href="#testtransaction" id="testtransaction"></a>

#### testTransaction

```http
HTTP POST /testTransaction
```

*   CURL command

    ```shell
    curl localhost:36002/testTransaction -X POST -d "{\"contract_address\":\"\",\"code\":\"\\\"use strict\\\";log(undefined);function query() { return 1; }\",\"input\":\"{}\",\"contract_balance\":\"100009000000\",\"fee_limit\":100000000000000000,\"gas_price\":1000,\"opt_type\":2,\"source_address\":\"\"}"
    ```
*   Function

    The evaluation fee is fixed and **does not depend on the account balance**. Both the **originating account** and the **target account** involved in a transaction must exist in the system. However, when creating a new account, the **target address** is not required to pre-exist in the system.
*   Request example:

    ```json
    {
      "items": [
        {
          "transaction_json": {
            "fee_limit": 99999999700110000,
            "gas_price": 1,
            "nonce": 1,
            "operations": [
              {
                "pay_coin": {
                  "amount": 299890000,
                  "dest_address": "ztxSdCZ13uZUWKv7AdhRcED6a9g7RjxpfKC1D"
                },
                "type": 7
              }
            ],
            "source_address": "ztxSkScnQJ1ciM1C9rBVw6DbtogbAcWFuXRhC"
          },
          "signature_number":1
        }
      ]
    }
    ```
*   Keywords in JSON

    <table><thead><tr><th width="191">Keyword</th><th width="107.3333740234375">Type</th><th>Description</th></tr></thead><tbody><tr><td>source_address</td><td>string</td><td>The original address of the simulated transaction refers to the <strong>sender's address</strong> that initiates the contract call during debugging.</td></tr><tr><td>nonce</td><td>int64</td><td>Unique transaction sequence number.</td></tr><tr><td>signature_number</td><td>int64</td><td>Signature count; defaults to 1 if unset.</td></tr><tr><td>metadata</td><td>string</td><td>Optional, the number of signatures</td></tr><tr><td>operations</td><td>array</td><td><strong>Operation List</strong>: The transaction's payload defining its intended actions. Refer to <strong>Operations Structure</strong> for details.</td></tr></tbody></table>
*   Return value

    ```json
    {
      "error_code": 0,
      "error_desc": "",
      "result": {
        "hash": "7f0d9de23d6d8f2964a1efe4a458e02e43e47f60f3c22bb132b676c54a44ba04",
        "logs": null,
        "query_rets": null,
        "stat": null,
        "txs": [
          {
            "actual_fee": 264,
            "gas": 264,
            "transaction_env": {
              "transaction": {
                "fee_limit": 99999999700110000,
                "gas_price": 1,
                "nonce": 1,
                "operations": [
                  {
                    "pay_coin": {
                      "amount": 299890000,
                      "dest_address": "ztxSdCZ13uZUWKv7AdhRcED6a9g7RjxpfKC1D"
                    },
                    "type": 7
                  }
                ],
                "source_address": "ztxSkScnQJ1ciM1C9rBVw6DbtogbAcWFuXRhC"
              }
            }
          }
        ]
      }
    }
    ```

### Transaction Structure

*   In JSON format

    ```json
    {
      "source_address":"ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV",//The source account, also called the originator of the transaction
      "nonce":2, //The nonce value of the source account
      "fee_limit" : 1000000, //The transaction fee that you intend to pay
      "gas_price": 1000,//The gas price (not less than the minimum configured)
      "ceil_ledger_seq": 100, //Optional, block height limit, if greater than 0, the transaction is only valid below (including the height) the block height
      "metadata":"0123456789abcdef", //Optional, a user-defined note for transactions, in hexadecimal format
      "operations":[
        {
          //Fill in according to specific operations
        },
        {
          //Fill in according to specific operations
        }
        ......
      ]
    }
    ```
*   Keywords in JSON

    <table><thead><tr><th width="189">Keyword</th><th width="108">Type</th><th>Description</th></tr></thead><tbody><tr><td>source_address</td><td>string</td><td>The original address of the simulated transaction refers to the <strong>sender's address</strong> that initiates the contract call during debugging.</td></tr><tr><td>nonce</td><td>int64</td><td>Unique transaction sequence number.</td></tr><tr><td>fee_limit</td><td>int64</td><td><p></p><p><strong>Fee Limit</strong>: The maximum fee allowed for the transaction.</p><ul><li>If successful, the actual fee is charged.</li><li>If failed, the full fee limit is charged.<br><strong>Unit</strong>: UGas (1 Gas = 10⁶ UGas).</li></ul></td></tr><tr><td>gas_price</td><td>int64</td><td><strong>Gas Price</strong>: Used to calculate the handling fee per operation and the transaction byte fee.<br><strong>Unit</strong>: UGas (1 Gas = 10⁶ UGas).</td></tr><tr><td>ceil_ledger_seq</td><td>int64</td><td><strong>Optional</strong>: Sets a block height limit for the transaction an advanced feature to control when the transaction can be included.</td></tr><tr><td>operations</td><td>array</td><td><strong>Operation List</strong>: Defines what the transaction is intended to perform. Refer to <strong>Operations Structure</strong> for detailed information.</td></tr><tr><td>metadata</td><td>string</td><td><strong>Optional</strong>: A user-defined field that can be left blank or used to include a note.</td></tr></tbody></table>

### &#x20;<a href="#operations-structure" id="operations-structure"></a>

### Operations Structure

The corresponding `operations` in the JSON structure of the transaction can contain one or more operations.

*   In JSON format

    ```json
    {
      "type": 1,//The operation type
      "source_address": "adxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV",//Optional, the source account of the operations 
      "metadata": "0123456789abcdef",//Optional, a user-defined note for transactions, in hexadecimal format
      "create_account": {
        //The parameters of the account to be created
      },
      "issue_asset": {
        //The parameters of the asset to be issued
      },
      "pay_asset": {
        //The parameters of the asset to be transferred
      },
      "set_metadata": {
        //Set the relevant parameters of the account metadata
      },
      "pay_coin": {
        //The parameters of the native token(Gas) to be transferred
      },
      "set_privilege": {
        ///The parameters of setting the account privileges
      },
      "log": {
        //The parameters of the log
      }
    }
    ```
*   Keywords in JSON

    <table><thead><tr><th width="182">Keyword</th><th width="111">Type</th><th>Description</th></tr></thead><tbody><tr><td>type</td><td>int</td><td><strong>Operation Code</strong>: Identifies the specific action to perform. Different codes correspond to different operations. See <strong>Operation Codes</strong> for more details.</td></tr><tr><td>source_address</td><td>string</td><td><strong>Optional</strong>: Specifies the source account for the operation. If left blank, it defaults to the transaction’s source account.</td></tr><tr><td>metadata</td><td>string</td><td><strong>Optional</strong>: A custom field that can remain empty or include a user-defined note.</td></tr><tr><td>create_account</td><td>JSON</td><td>Creating Accounts operation</td></tr><tr><td>issue_asset</td><td>JSON</td><td>Issuing Assets operation</td></tr><tr><td>pay_asset</td><td>JSON</td><td>Transferring Assets operation</td></tr><tr><td>set_metadata</td><td>JSON</td><td>Setting Metadata operation</td></tr><tr><td>pay_coin</td><td>JSON</td><td>Transferring Gas Assets operation</td></tr><tr><td>log</td><td>JSON</td><td>Recording Logs operation</td></tr><tr><td>set_privilege</td><td>JSON</td><td>Setting Privileges operation</td></tr></tbody></table>

#### &#x20;<a href="#operation-codes" id="operation-codes"></a>

#### Operation Codes

<table><thead><tr><th width="156.33333333333331">Operation Code</th><th width="187">Operation</th><th>Description</th></tr></thead><tbody><tr><td>1</td><td>create_account</td><td>Creating Accounts</td></tr><tr><td>2</td><td>issue_asset</td><td>Issuing Assets</td></tr><tr><td>3</td><td>pay_asset</td><td>Transferring Assets</td></tr><tr><td>4</td><td>set_metadata</td><td>Setting Metadata</td></tr><tr><td>7</td><td>pay_coin</td><td>Transferring Gas Assets</td></tr><tr><td>8</td><td>log</td><td>Recording Logs</td></tr><tr><td>9</td><td>set_privilege</td><td>Setting Privileges</td></tr></tbody></table>

#### &#x20;<a href="#creating-accounts" id="creating-accounts"></a>

#### Creating Accounts

The source account creates a new account on the blockchain. Creating Accounts are divided into Creating Normal Accounts and Creating Contract Accounts.



**Creating Normal Accounts**

> **Note**: Both `master_weight` and `tx_threshold` must be 1 in the current operation.

*   In JSON format

    ```json
    {
      "dest_address": "ztxSfW5K2GkmzNgBG95qjyX2tgZezmUCHYMfp",//The target account address to be created
      "init_balance": 100000,//The initial balance of the target account
      "priv":  {
        "master_weight": 1,//The weight owned by the target account
        "thresholds": {
          "tx_threshold": 1//The threshold required to initiate a transaction
        }
      }
    }
    ```
*   Keywords in JSON

    <table><thead><tr><th width="157">Keyword</th><th width="105">Type</th><th>Description</th></tr></thead><tbody><tr><td>dest_address</td><td>string</td><td><strong>Target Account Address</strong>: Required when creating a normal account; this field cannot be left empty.</td></tr><tr><td>init_balance</td><td>int64</td><td>The initial Gas value of the target account, in UGas, 1 Gas = 10^6 UGas</td></tr><tr><td>master_weight</td><td>int64</td><td>The master weight of the target account, which ranges [0, MAX(UINT32)]</td></tr><tr><td>tx_threshold</td><td>int64</td><td>The threshold for initiating a transaction below which the transaction cannot be initiated, which ranges ​​[0, MAX(INT64)]</td></tr></tbody></table>
*   Complete transaction structure

    ```json
    {
      "source_address":"ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV",//The source account, also called the originator of the transaction
      "nonce":2, //The nonce value of the source account
      "fee_limit" : 1000000, //The transaction fee that you intend to pay
      "gas_price": 1000,//The gas price (not less than the minimum configured)
      "ceil_ledger_seq": 100, //Optional, block height limit, if greater than 0, the transaction is only valid below (including the height) the block height
      "metadata":"0123456789abcdef", //Optional, a user-defined note for transactions, in hexadecimal format
      "operations":[
        {
          "type": 1,//The create_account operation type
          "source_address": "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV",//Optional, the source account of the operations
          "metadata": "0123456789abcdef",//Optional, a user-defined note for transactions, in hexadecimal format
          "create_account": {
            "dest_address": "ztxSfW5K2GkmzNgBG95qjyX2tgZezmUCHYMfp",//The target account address to be created
            "init_balance": 100000,//The initial balance of the target account
            "priv":  {
              "master_weight": 1,//The weight owned by the target account
              "thresholds": {
                "tx_threshold": 1//The threshold required to initiate a transaction
              }
            }
          }
        }
      ]
    }
    ```
*   Query

    The account information is queried through the getAccount interface.



**Creating Contract Accounts**

> **Note**: In the current operation, `master_weight` must be 0 and `tx_threshold` must be 1.

*   In JSON format

    ```json
    {
        "contract": { //Contract
            "payload": "
                'use strict';
                function init(bar)
                {
                  return;
                }

                function main(input)
                {
                  return;
                }

                function query()
                {
                  return;
                }
              "//Contract code
            },
            "init_balance": 100000,//The initial asset of the contract account 
            "init_input" : "{\"method\":\"toWen\",\"params\":{\"feeType\":0}}",//Optional, the entry of the init function
            "priv": {
                "master_weight": 0,//The weight of the contract account to be created
                "thresholds": {
                "tx_threshold": 1 //The weight required to initiate a transaction
            }
        }
    }
    ```
*   Keywords in JSON

    <table><thead><tr><th width="159">Keyword</th><th width="114">Type</th><th>Description</th></tr></thead><tbody><tr><td>payload</td><td>string</td><td>Contract code</td></tr><tr><td>init_balance</td><td>int64</td><td>The initial Gas value of the target account, in UGas, 1 Gas = 10^6 UGas</td></tr><tr><td>init_input</td><td>string</td><td><strong>Optional</strong>: Specifies the input parameters for the <code>init</code> function in the contract code.</td></tr><tr><td>master_weight</td><td>int64</td><td><strong>Master Weight</strong>: Defines the main authority level of the target account.</td></tr><tr><td>tx_threshold</td><td>int64</td><td>The threshold for initiating a transaction below which it is not possible to initiate a transaction.</td></tr></tbody></table>
*   Complete transaction structure

    ```json
    {
      "source_address":"ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV",//The source account, also called the originator of the transaction
      "nonce":2, //The nonce value of the source account
      "fee_limit" : 1000000, //The transaction fee that you intend to pay
      "gas_price": 1000,//The gas price (not less than the minimum configured)
      "ceil_ledger_seq": 100, //Optional, block height limit, if greater than 0, the transaction is only valid below (including the height) the block height
      "metadata":"0123456789abcdef", //Optional, a user-defined note for transactions, in hexadecimal format
      "operations":[
        {
          "type": 1,//The create_account operation type
          "source_address": "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV",//Optional, the source account of the operations
          "metadata": "0123456789abcdef",//Optional, a user-defined note for transactions, in hexadecimal format
          "create_account": {
            "contract": { //Contract
              "payload": "
                'use strict';
                function init(bar)
                {
                  return;
                }

                function main(input)
                {
                  return;
                }

                function query()
                {
                  return;
                }
              "//Contract code
            },
            "init_balance": 100000,//The initial asset of the contract account 
            "init_input" : "{\"method\":\"toWen\",\"params\":{\"feeType\":0}}",//Optional, the entry of the init function
            "priv": {
              "master_weight": 0,//The weight of the contract account to be created
              "thresholds": {
                "tx_threshold": 1 //The weight required to initiate a transaction
              }
            }
          }
        }
      ]
    }
    ```
* Query
  * The account information is queried through the getAccount interface.
  *   Query with the getTransactionHistory interface, and the result is as follows:

      ```json
      [
        {
          "contract_address": "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV", //The contract account
          "operation_index": 0                                        //The operation index value in the transaction array, 0 means the first transaction
        }
      ]
      ```

#### Issuing Assets

*   Function

    The source account in this operation issues a digital asset, which will appear in its asset balance upon successful execution.
*   In JSON format

    ```json
    {
      "code": "HYL", //The code of the asset to be issued
      "amount": 1000 //The amount of the asset to be issued
    }
    ```
*   Keywords in JSON

    <table><thead><tr><th width="120">Keyword</th><th width="113">Type</th><th>Description</th></tr></thead><tbody><tr><td>code</td><td>string</td><td>The code of the asset to be issued, which ranges [1, 64]</td></tr><tr><td>amount</td><td>int64</td><td>The amount of the asset to be issued, which ranges (0, MAX(int64))</td></tr></tbody></table>
*   Complete transaction structure

    ```json
      {
      "source_address":"ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV",//The source account, also called the originator of the transaction
      "nonce":2, //The nonce value of the source account
      "fee_limit" : 1000000, //The transaction fee that you intend to pay
      "gas_price": 1000,//The gas price (not less than the minimum configured)
      "ceil_ledger_seq": 100, //Optional, block height limit, if greater than 0, the transaction is only valid below (including the height) the block height
      "metadata":"0123456789abcdef", //Optional, a user-defined note for transactions, in hexadecimal format
      "operations":[
        {
          "type": 2,//The issue_asset operation type
          "source_address": "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV",//Optional, the source account of the operations
          "metadata": "0123456789abcdef",//Optional, a user-defined note for transactions, in hexadecimal format
          "issue_asset": {
            "code": "HYL", //The code of the asset to be issued
            "amount": 1000 //The amount of the asset to be issued
          }
        }
      ]
    }
    ```

#### Transferring Assets

> **Note**: If the target account is a contract account, the current operation triggers the contract execution of the target account.

*   Function

    The source account of this operation transfers an asset to the target account.
*   In JSON format

    ```json
    {
      "dest_address": "ztxSmcbrEqbnNKWxfeWCb8kHR8zGBxdviJ7Pd",//The target account to receive the asset
      "asset": {
        "key": {
          "issuer": "ztxShgsZCaSrAQY3fS4xnvSruA2a9yBeopUYy",//The account to issue the asset
          "code": "HYL" // The code of the asset to be transferred
        },
        "amount": 100 //The amount of the asset to be transferred
      },
      "input": "{\"bar\":\"foo\"}"　// Optional, the input parameters of the main code of the contract code in the target account
    }
    ```
*   Keywords in JSON

    <table><thead><tr><th width="166">Keyword</th><th width="105">Type</th><th>Description</th></tr></thead><tbody><tr><td>dest_address</td><td>string</td><td>Address of the target account</td></tr><tr><td>issuer</td><td>string</td><td>Address of the issuer</td></tr><tr><td>code</td><td>string</td><td>Asset code which ranges [1, 64]</td></tr><tr><td>amount</td><td>int64</td><td>Amount of the asset which ranges (0,MAX(int64))</td></tr><tr><td>input</td><td>string</td><td><strong>Optional</strong>: If the target account is a contract, the input is passed to the <code>main</code> function's argument. This setting is ignored for normal accounts.</td></tr></tbody></table>
*   Complete transaction structure

    ```json
    {
      "source_address":"ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV",//The source account, also called the originator of the transaction
      "nonce":2, //The nonce value of the source account
      "fee_limit" : 1000000,//The transaction fee that you intend to pay
      "gas_price": 1000,//The gas price (not less than the minimum configured)
      "ceil_ledger_seq": 100,//Optional, block height limit, if greater than 0, the transaction is only valid below (including the height) the block height
      "metadata":"0123456789abcdef",//Optional, a user-defined note for transactions, in hexadecimal format
      "operations":[
        {
          "type": 3,//The pay_asset operation type
          "source_address": "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV",//Optional, the source account of the operations
          "metadata": "0123456789abcdef",//Optional, a user-defined note for transactions, in hexadecimal format
          "pay_asset": {
            "dest_address": "ztxSmcbrEqbnNKWxfeWCb8kHR8zGBxdviJ7Pd",//The target account to receive the asset
            "asset": {
              "key": {
                "issuer": "ztxShgsZCaSrAQY3fS4xnvSruA2a9yBeopUYy",//The account to issue the asset
                "code": "HYL" // The code of the asset to be transferred
              },
              "amount": 100 //The amount of the asset to be transferred
            },
            "input": "{\"bar\":\"foo\"}" // Optional, the input parameters of the main code of the contract code in the target account
          }
        }
      ]
    }
    ```

#### Setting Metadata

*   Function

    The source account of this operation modifies or adds metadata to the metadata table.
*   In JSON format

    ```json
    {
      "key": "abc",//Metadata Keyword
      "value": "hello abc!",//Metadata content
      "version": 0 //Optional, the metadata version
    }
    ```
*   Keywords in JSON

    <table><thead><tr><th width="143">Keyword</th><th width="131">Type</th><th>Description</th></tr></thead><tbody><tr><td>key</td><td>string</td><td>Keyword of metadata, which ranges (0, 1024].</td></tr><tr><td>value</td><td>string</td><td>Content of metadata, which ranges [0, 256K].</td></tr><tr><td>version</td><td>int64</td><td><p></p><p><strong>Optional</strong>: Metadata version number, default is <code>0</code>.</p><ul><li><code>0</code>: No version limit</li><li><code>>0</code>: Must match the specified version</li><li><code>&#x3C;0</code>: Invalid value</li></ul></td></tr></tbody></table>
*   Complete transaction structure

    ```json
    {
    "source_address":"ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV",//The source account, also called the originator of the transaction
    "nonce":2, //The nonce value of the source account
    "fee_limit" : 1000000, //The transaction fee that you intend to pay
    "gas_price": 1000,//The gas price (not less than the minimum configured)
    "ceil_ledger_seq": 100, //Optional, block height limit, if greater than 0, the transaction is only valid below (including the height) the block height
    "metadata":"0123456789abcdef", //Optional, a user-defined note for transactions, in hexadecimal format
    "operations":[
      {
        "type": 4,//The set_metadata operation type
        "source_address": "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV",//Optional, the source account of the operations
        "metadata": "0123456789abcdef",//Optional, a user-defined note for transactions, in hexadecimal format
        "set_metadata": {
          "key": "abc",//Metadata Keyword
          "value": "hello abc!",//Metadata content
          "version": 0 //Optional, the metadata version
        }
      }
    ]
    }
    ```

#### Setting Privileges

*   Function

    Set the **signer weights** and the **thresholds** required for different operations. For more details, refer to **Assignment of Control Rights**.
*   In JSON format

    ```json
    {
      "master_weight": "10",//Optional, the current account's weight
      "signers": //Optional, a list of signers that need to operate
      [
        {
          "address": "ztxSWriszctU64GQAKdVjCCgW7bSsTb8repJk",//The signer's address
          "weight": 8　//Optional, the signer's weight
        }
      ],
      "tx_threshold": "2",//Optional, the threshold required to initiate the transaction
      "type_thresholds": //Optional, the thresholds required for different operations
      [
        {
          "type": 1,//The type of account creation
          "threshold": 8 //Optional, the threshold required for this operation
        },
        {
          "type": 2,//The type of asset issuance
          "threshold": 9 //Optional, the threshold required for this operation
        }
      ]
    }
    ```
*   Keywords in JSON

    <table><thead><tr><th width="164">Keyword</th><th width="108">Type</th><th>Description</th></tr></thead><tbody><tr><td>master_weight</td><td>string</td><td><p><strong>Optional</strong>: Defaults to <code>""</code>, which refers to the account’s master weight.</p><ul><li><code>""</code>: Do not modify the value</li><li><code>"0"</code>: Set master weight to 0</li><li><code>"1"</code> to <code>"MAX(UINT32)"</code>: Set weight to the specified value</li><li>Any other input: Invalid value</li></ul></td></tr><tr><td>signers</td><td>array</td><td><strong>Optional</strong>: A list of signers involved in the operation. Defaults to an empty object, which means no signers are set.</td></tr><tr><td>address</td><td>string</td><td><strong>Signer Address</strong>: The address of the signer to be set, which must comply with the address verification rules.</td></tr><tr><td>weight</td><td>int64</td><td><p><strong>Optional</strong>: Defaults to <code>0</code>.</p><ul><li><code>0</code>: Deletes the signer</li><li><code>(0, MAX(UINT32)]</code>: Sets the signer’s weight to the specified value</li><li>Other values: Invalid</li></ul></td></tr><tr><td>tx_threshold</td><td>string</td><td><p><strong>Optional</strong>: Defaults to <code>""</code>, indicating the account’s minimum privilege (<code>tx_threshold</code>).</p><ul><li><code>""</code>: Do not modify the value</li><li><code>"0"</code>: Set <code>tx_threshold</code> to 0</li><li><code>"1"</code> to <code>"MAX(INT64)"</code>: Set to specified weight</li><li>Other values: Invalid</li></ul></td></tr><tr><td>type_thresholds</td><td>array</td><td><strong>Optional</strong>: A list of thresholds required for different operations. Defaults to an empty object, which means no thresholds are set.</td></tr><tr><td>type</td><td>int</td><td>To indicate a certain operation type (0, 100]</td></tr><tr><td>threshold</td><td>int64</td><td><p><strong>Optional</strong>: Defaults to <code>0</code>.</p><ul><li><code>0</code>: Deletes the operation type</li><li><code>(0, MAX(INT64)]</code>: Sets the operation’s threshold to the specified value</li><li>Other values: Invalid</li></ul></td></tr></tbody></table>
*   Complete transaction structure

    ```json
    {
    "source_address":"ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV",//The source account, also called the originator of the transaction
    "nonce":2, //The nonce value of the source account
    "fee_limit" : 1000000, //The transaction fee that you intend to pay
    "gas_price": 1000,//The gas price (not less than the minimum configured)
    "ceil_ledger_seq": 100, //Optional, block height limit, if greater than 0, the transaction is only valid below (including the height) the block height
    "metadata":"0123456789abcdef", //Optional, a user-defined note for transactions, in hexadecimal format
    "operations":[
      {
        "type": 9,//The set_privilege operation type
        "source_address": "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV",//Optional, the source account of the operations
        "metadata": "0123456789abcdef",//Optional, a user-defined note for transactions, in hexadecimal format
        "set_privilege": {
          "master_weight": "10",//Optional, the current account's weight
          "signers"://Optional, a list of signers that need to operate
          [
            {
              "address": "ztxSWriszctU64GQAKdVjCCgW7bSsTb8repJk",//The signer's address
              "weight": 8 //Optional, the signer's weight
            }
          ],
          "tx_threshold": "2",//Optional, the threshold required to initiate the transaction
          "type_thresholds"://Optional, the thresholds required for different operations
          [
            {
              "type": 1,//The type of account creation
              "threshold": 8 //Optional, the threshold required for this operation
            },
            {
              "type": 2,//The type of asset issuance
              "threshold": 9 //Optional, the threshold required for this operation
            }
          ]
        }
      }
    ]
    }
    ```

#### Transferring Gas Assets

> **Note**: If the target account is a contract account, the current operation triggers the contract execution of the target account.

*   Function

    Two functions:

    1. The source account of this operation transfers a Gas asset to the target account.
    2. The source account of this operation creates a new account on the blockchain.
*   In JSON format

    ```json
    {
      "dest_address": "ztxSgGkHuPv6FQUu6C5uWnaqyKJ22bSNrJUCJ",//The target account to receive Gas assets
      "amount": 100,//The amount of Gas assets to be transferred
      "input": "{\"bar\":\"foo\"}" // Optional, the input parameters of the main code of the contract code in the target account
    }
    ```
*   Keywords in JSON

    <table><thead><tr><th width="136">Keyword</th><th width="99">Type</th><th>Description</th></tr></thead><tbody><tr><td>dest_address</td><td>string</td><td>The target account</td></tr><tr><td>amount</td><td>array</td><td><strong>Optional</strong>: A list of signers to be configured. Defaults to an empty object, which means no signer settings will be applied.</td></tr><tr><td>input</td><td>string</td><td><strong>Optional</strong>: If the target is a contract account, the input is passed to the <code>main</code> function of the contract code. This setting is ignored for normal accounts.</td></tr></tbody></table>
*   Complete transaction structure

    ```json
    {
    "source_address":"ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV",//The source account, also called the originator of the transaction
    "nonce":2, //The nonce value of the source account
    "fee_limit" : 1000000, //The transaction fee that you intend to pay
    "gas_price": 1000,//The gas price (not less than the minimum configured)
    "ceil_ledger_seq": 100, //Optional, block height limit, if greater than 0, the transaction is only valid below (including the height) the block height
    "metadata":"0123456789abcdef", //Optional, a user-defined note for transactions, in hexadecimal format
    "operations":[
      {
        "type": 7,//The pay_coin operation type
        "source_address": "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV",//Optional, the source account of the operations 
        "metadata": "0123456789abcdef",//Optional, a user-defined note for transactions, in hexadecimal format
        "pay_coin": {
          "dest_address": "ztxSgGkHuPv6FQUu6C5uWnaqyKJ22bSNrJUCJ",//The target account to receive Gas assets
          "amount": 100,//The amount of Gas assets to be transferred
          "input": "{\"bar\":\"foo\"}" // Optional, the input parameters of the main code of the contract code in the target account
        }
      }
    ]
    }
    ```

#### Recording Logs

*   Function

    The source account of this operation writes the log to the blockchain.
*   In JSON format

    ```json
    {
      "topic": "hello",// The topic of the log
      "datas"://The content of the log
      [
        "hello, log 1",
        "hello, log 2"
      ]
    }
    ```
*   Keywords in JSON

    <table><thead><tr><th width="117">Keyword</th><th width="112">Type</th><th>Description</th></tr></thead><tbody><tr><td>topic</td><td>string</td><td>Log topic and the parameter length is (0,128]</td></tr><tr><td>datas</td><td>array</td><td>Log content. The length of each element is (0,1024]</td></tr></tbody></table>
*   Complete transaction structure

    ```json
    {
      "source_address":"ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV",//The source account, also called the originator of the transaction
      "nonce":2, //The nonce value of the source account
      "fee_limit" : 1000000, //The transaction fee that you intend to pay
      "gas_price": 1000,//The gas price (not less than the minimum configured)
      "ceil_ledger_seq": 100, //Optional, block height limit, if greater than 0, the transaction is only valid below (including the height) the block height
      "metadata":"0123456789abcdef", //Optional, a user-defined note for transactions, in hexadecimal format
      "operations":[
        {
          "type": 8,//The log operation type
          "source_address": "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV",//Optional, the source account of the operations 
          "metadata": "0123456789abcdef",//Optional, a user-defined note for transactions, in hexadecimal format
          "log": {
            "topic": "hello",// The topic of the log
            "datas"://The content of the log
            [
              "hello, log 1",
              "hello, log 2"
            ]
          }
        }
      ]
    }
    ```

### Assignment of Control Rights

When creating an account, you can define its control rights using the `priv` field. This allows you to assign signer weights and operation thresholds.

```json
{
  "master_weight": "70",//The weight for the private key of this address is 70
  "signers": [//The weights assigned
    {
      "address": "ztxSigpdiWcW22hw54i9PEFk1s4WLEy5Rmh6K",
      "weight": 55    //The weight of the above address is 55
    },
    {
      "address": "ztxSX1SypC33doY4a5MYYr5zBZUvB33kKHdAo",
      "weight": 100    //The weight of the above address is 100
    }
  ],
  "tx_threshold": "8",//The weight required to initiate the transaction is 8
  "type_thresholds": [
    {
      "type": 1,//The weight required to create an account is 11
      "threshold": 11
    },
    {//The weight required to issue an asset is 21
      "type": 2,
      "threshold": 21
    },
    {//The weight required to transfer assets is 31
      "type": 3,
      "threshold": 31
    },
    {//The weight required to set metadata is 41
      "type": 4,
      "threshold": 41
    },
    {//The weight required to change controllers is 51
      "type": 5,
      "threshold": 51
    },
    {//The weight required to change operations is 61
      "type": 6,
      "threshold": 61
    }
  ]
}
```

### Example

In this example, the account **`ztxSW512jUyDASna878ECgZ4QFyhh6AtEJP6E`** initiates a transaction containing a single operation:

* **Create a new account** with address **`ztxSX1SypC33doY4a5MYYr5zBZUvB33kKHdAo`**
* The account creation is completed by **transferring Gas** to the new account during the operation.

#### Assembling Transactions

Referring to the structure of Transaction Structure, the following three steps are required:

1. Obtaining the account nonce value
2. Assembling operations
3. Generating transaction objects

**Obtaining the Account Nonce Value**

In a transaction structure, it's essential to confirm the **serial number (`nonce`)** of the initiating account. To do this:

1. Use the **`getAccountBase`** interface to retrieve the current `nonce` value of the initiating account.
2. **Increment the `nonce` by 1** to set the correct value for the new transaction.

This ensures transaction ordering and prevents replay.

The interface call is as follows:

```http
HTTP GET https://{endpoint}/getAccountBase?address=ztxSW512jUyDASna878ECgZ4QFyhh6AtEJP6E
or
curl get https://{endpoint}/getAccountBase?address=ztxSW512jUyDASna878ECgZ4QFyhh6AtEJP6E
```

The following content is returned:

```json
{
  "error_code" : 0,
  "result" : {
    "address" : "ztxSW512jUyDASna878ECgZ4QFyhh6AtEJP6E",
    "assets_hash" : "ad67d57ae19de8068dbcd47282146bd553fe9f684c57c8c114453863ee41abc3",
    "balance" : 96545066100,
    "metadatas_hash" : "ad67d57ae19de8068dbcd47282146bd553fe9f684c57c8c114453863ee41abc3",
    "nonce" : 20,
    "priv" : {
      "master_weight" : 1,
      "thresholds" : {
        "tx_threshold" : 1
      }
    }
  }
}
```

**Assembling Operations**

According to the structure of Operations Structure, Operation Codes, and Transferring Gas Assets structure, the JSON format of the generation operation is as follows:

```json
{
  "type": 7,
  "pay_coin": {
    "dest_address": "ztxSX1SypC33doY4a5MYYr5zBZUvB33kKHdAo",
    "amount": 10000000,
    "input": ""
  }
}
```

**Generating Transaction Objects**

In this example, after obtaining the account's nonce value of **20**, the **new transaction’s serial number** is set to **21**. Based on the assembled operation structure, the transaction JSON format will look like this:

```json
{
  "source_address": "ztxSW512jUyDASna878ECgZ4QFyhh6AtEJP6E",
  "nonce": 21,
  "fee_limit":1000000,
  "gas_price":1000,
  "operations": [{
    "type": 7,
    "pay_coin": {
      "dest_address": "ztxSX1SypC33doY4a5MYYr5zBZUvB33kKHdAo",
      "amount": 10000000,
      "input": ""
    }
  }]
}
```

#### Serializing Transaction Data

This is done through the `getTransactionBlob` interface.

The interface call is as follows:

```http
HTTP POST https://{endpoint}/getTransactionBlob
or
curl https://{endpoint}/getTransactionBlob -X POST -d "{\"source_address\":\"ztxSW512jUyDASna878ECgZ4QFyhh6AtEJP6E\",\"nonce\":21,\"fee_limit\":1000000,\"gas_price\":1000,\"operations\":[{\"type\":7,\"pay_coin\":{\"dest_address\":\"ztxSX1SypC33doY4a5MYYr5zBZUvB33kKHdAo\",\"amount\":10000000,\"input\":\"\"}}]}"
```

Return the following content:

```json
{
  "error_code": 0,
  "error_desc": "",
  "result": {
    "hash": "3f90865062d7737904ea929cbde7c45e831e4972cf582b69a0198781c452e9e1",
    "transaction_blob": "0a246275516f50326552796d4163556d33757657675138526e6a7472536e58425866417a7356101518c0843d20e8073a2f0807522b0a24627551747336446654354b6176745639344a675a79373548397069776d62374b6f5557671080ade204"
  }
}
```

#### &#x20;<a href="#signing-signatures" id="signing-signatures"></a>

#### Signing Signatures

Signing signatures is to sign the value of `transaction_blob` in `getTransactionBlob` with the private key of `ztxSW512jUyDASna878ECgZ4QFyhh6AtEJP6E` and generate a public key. For details, please refer to keypair. The resulting signature data is as follows:

```json
［{
  "sign_data": "ACF64A7D41244AFC4465DBC225D616E0499776140D46BA7A84B1B6B263DAF1422904137E0181301F480F7114EC7CC5BBE4763EDA981E565EF81EF7705596CB0B",
  "public_key": "b00168eceea7900ddcb8f694161755f98590ba7944de3bfe339610fe0cacc10a18372dcbf71b"
}］
```

#### Submitting Transaction Data

According to the signature data obtained by Signing Signatures and the `transaction_blob` obtained by Serializing Transaction Data, the JSON format of the body in the `submitTransaction` interface is generated as follows:

```json
{
  "items" : [{
    "transaction_blob" : "0a246275516f50326552796d4163556d33757657675138526e6a7472536e58425866417a7356101518c0843d20e8073a2f0807522b0a24627551747336446654354b6176745639344a675a79373548397069776d62374b6f5557671080ade204",
    "signatures" : [{
      "sign_data" : "ACF64A7D41244AFC4465DBC225D616E0499776140D46BA7A84B1B6B263DAF1422904137E0181301F480F7114EC7CC5BBE4763EDA981E565EF81EF7705596CB0B",
      "public_key" : "b00168eceea7900ddcb8f694161755f98590ba7944de3bfe339610fe0cacc10a18372dcbf71b"
    }]
  }]
}
```

The interface call is as follows:

```http
HTTP POST https://{endpoint}/submitTransaction
or
curl https://{endpoint}/submitTransaction -X POST -d "{\"items\":[{\"transaction_blob\":\"0a246275516f50326552796d4163556d33757657675138526e6a7472536e58425866417a7356101518c0843d20e8073a2f0807522b0a24627551747336446654354b6176745639344a675a79373548397069776d62374b6f5557671080ade204\",\"signatures\":[{\"sign_data\":\"ACF64A7D41244AFC4465DBC225D616E0499776140D46BA7A84B1B6B263DAF1422904137E0181301F480F7114EC7CC5BBE4763EDA981E565EF81EF7705596CB0B\",\"public_key\":\"b00168eceea7900ddcb8f694161755f98590ba7944de3bfe339610fe0cacc10a18372dcbf71b\"}]}]}"
```

Return the following content:

```json
{
  "results": [
    {
      "error_code": 0,
      "error_desc": "",
      "hash": "3f90865062d7737904ea929cbde7c45e831e4972cf582b69a0198781c452e9e1"
    }
  ],
  "success_count": 1
}
```

#### Querying the Transaction Result

According to the hash obtained by the `submitTransaction` interface, confirm whether the transaction is executed successfully by the `getTransactionHistory` interface (check whether `error_code` under `transactions` is equal to 0).

The interface call is as follows:

```http
HTTP GET https://{endpoint}/getTransactionHistory?hash=3f90865062d7737904ea929cbde7c45e831e4972cf582b69a0198781c452e9e1
or
curl get https://{endpoint}/getTransactionHistory?hash=3f90865062d7737904ea929cbde7c45e831e4972cf582b69a0198781c452e9e1
```

Return the following result:

```json
{
  "error_code": 0,//The transaction is queried
  "result": {
    "total_count": 1,
    "transactions": [
      {
        "actual_fee": 245000,
        "close_time": 1552125554325904,
        "error_code": 0,//The transaction is executed successfully
        "error_desc": "",
        "hash": "3f90865062d7737904ea929cbde7c45e831e4972cf582b69a0198781c452e9e1",
        "ledger_seq": 2688046,
        "signatures": [
          {
            "public_key": "b00168eceea7900ddcb8f694161755f98590ba7944de3bfe339610fe0cacc10a18372dcbf71b",
            "sign_data": "acf64a7d41244afc4465dbc225d616e0499776140d46ba7a84b1b6b263daf1422904137e0181301f480f7114ec7cc5bbe4763eda981e565ef81ef7705596cb0b"
          }
        ],
        "transaction": {
          "fee_limit": 1000000,
          "gas_price": 1000,
          "nonce": 21,
          "operations": [
            {
              "pay_coin": {
                "amount": 10000000,
                "dest_address": "ztxSX1SypC33doY4a5MYYr5zBZUvB33kKHdAo"
              },
              "type": 7
            }
          ],
          "source_address": "ztxSW512jUyDASna878ECgZ4QFyhh6AtEJP6E"
        },
        "tx_size": 245
      }
    ]
  }
}
```

### Error Codes

The error code is composed of two parts:

* error\_code : Error code, approximate error classification
* error\_desc : Error Description, which can accurately find the error specific information from the error description

The error list is as follows:

<table><thead><tr><th width="107.33333333333331">Error code</th><th width="433">Name</th><th>Description</th></tr></thead><tbody><tr><td>0</td><td>ERRCODE_SUCCESS</td><td>Successful operation</td></tr><tr><td>1</td><td>ERRCODE_INTERNAL_ERROR</td><td>Inner service defect</td></tr><tr><td>2</td><td>ERRCODE_INVALID_PARAMETER</td><td>Parameters error</td></tr><tr><td>3</td><td>ERRCODE_ALRGasDY_EXIST</td><td>Objects already exist, such as repeated transactions submitted</td></tr><tr><td>4</td><td>ERRCODE_NOT_EXIST</td><td>Objects do not exist, such as null account, transactions and blocks etc</td></tr><tr><td>5</td><td>ERRCODE_TX_TIMEOUT</td><td>Transactions expired. It means the transaction has been removed from the buffer, but it still has probability to be executed</td></tr><tr><td>7</td><td>ERRCODE_MATH_OVERFLOW</td><td>Math calculation is overflown</td></tr><tr><td>20</td><td>ERRCODE_EXPR_CONDITION_RESULT_FALSE</td><td>The expression returns false. It means the TX failed to be executed currently, but it still has probability to be executed in the following blocks</td></tr><tr><td>21</td><td>ERRCODE_EXPR_CONDITION_SYNTAX_ERROR</td><td>The syntax of the expression returns is false. It means that the TX must fail</td></tr><tr><td>90</td><td>ERRCODE_INVALID_PUBKEY</td><td>Invalid public key</td></tr><tr><td>91</td><td>ERRCODE_INVALID_PRIKEY</td><td>Invalid private key</td></tr><tr><td>92</td><td>ERRCODE_ASSET_INVALID</td><td>Invalid assets</td></tr><tr><td>93</td><td>ERRCODE_INVALID_SIGNATURE</td><td>The weight of the signature does not match the threshold of the operation</td></tr><tr><td>94</td><td>ERRCODE_INVALID_ADDRESS</td><td>Invalid address</td></tr><tr><td>97</td><td>ERRCODE_MISSING_OPERATIONS</td><td>Absent operation of TX</td></tr><tr><td>98</td><td>ERRCODE_TOO_MANY_OPERATIONS</td><td>Over 100 operations in a single transaction</td></tr><tr><td>99</td><td>ERRCODE_BAD_SEQUENCE</td><td>Invalid sequence or nonce of TX</td></tr><tr><td>100</td><td>ERRCODE_ACCOUNT_LOW_RESERVE</td><td>Low reserve in the account</td></tr><tr><td>101</td><td>ERRCODE_ACCOUNT_SOURCEDEST_EQUAL</td><td>Sender and receiver accounts are the same</td></tr><tr><td>102</td><td>ERRCODE_ACCOUNT_DEST_EXIST</td><td>The target account already exists</td></tr><tr><td>103</td><td>ERRCODE_ACCOUNT_NOT_EXIST</td><td>Accounts do not exist</td></tr><tr><td>104</td><td>ERRCODE_ACCOUNT_ASSET_LOW_RESERVE</td><td>Low reserve in the account</td></tr><tr><td>105</td><td>ERRCODE_ACCOUNT_ASSET_AMOUNT_TOO_LARGE</td><td>Amount of assets exceeds the limitation ( int64 )</td></tr><tr><td>106</td><td>ERRCODE_ACCOUNT_INIT_LOW_RESERVE</td><td>Insufficient initial reserve for account creation</td></tr><tr><td>111</td><td>ERRCODE_FEE_NOT_ENOUGH</td><td>Low transaction fee</td></tr><tr><td>114</td><td>ERRCODE_OUT_OF_TXCACHE</td><td>TX buffer is full</td></tr><tr><td>120</td><td>ERRCODE_WEIGHT_NOT_VALID</td><td>Invalid weight</td></tr><tr><td>121</td><td>ERRCODE_THRESHOLD_NOT_VALID</td><td>Invalid threshold</td></tr><tr><td>144</td><td>ERRCODE_INVALID_DATAVERSION</td><td>Invalid data version of metadata</td></tr><tr><td>146</td><td>ERRCODE_TX_SIZE_TOO_BIG</td><td>TX exceeds upper limitation</td></tr><tr><td>151</td><td>ERRCODE_CONTRACT_EXECUTE_FAIL</td><td>Failure in contract execution</td></tr><tr><td>152</td><td>ERRCODE_CONTRACT_SYNTAX_ERROR</td><td>Failure in syntax analysis</td></tr><tr><td>153</td><td>ERRCODE_CONTRACT_TOO_MANY_RECURSION</td><td>The depth of contract recursion exceeds upper limitation</td></tr><tr><td>154</td><td>ERRCODE_CONTRACT_TOO_MANY_TRANSACTIONS</td><td>the TX submitted from the contract exceeds upper limitation</td></tr><tr><td>155</td><td>ERRCODE_CONTRACT_EXECUTE_EXPIRED</td><td>Contract expired</td></tr><tr><td>160</td><td>ERRCODE_TX_INSERT_QUEUE_FAIL</td><td>Failed to insert the TX into buffer</td></tr></tbody></table>
