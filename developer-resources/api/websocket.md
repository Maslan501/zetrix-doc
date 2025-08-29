# Websocket

### Websocket Interfaces

The WebSocket interface of Zetrix processes a variety of **predefined message types**, enabling real-time communication for tasks such as transaction submission, event subscription, and status updates. Each message type corresponds to a specific action or query within the Zetrix network protocol.

#### Message Type

```protobuf
enum ChainMessageType {
    CHAIN_TYPE_NONE = 0;
    CHAIN_HELLO = 10; // response with CHAIN_STATUS = 2;
    CHAIN_TX_STATUS = 11;
    CHAIN_PEER_ONLINE = 12;
    CHAIN_PEER_OFFLINE = 13;
    CHAIN_PEER_MESSAGE = 14;
    CHAIN_SUBMITTRANSACTION = 15;
    CHAIN_LEDGER_HGasDER = 16; //Zetrix notifies the client ledger(protocol::LedgerHeader) when closed
    CHAIN_SUBSCRIBE_TX = 17; //response with CHAIN_RESPONSE
    CHAIN_TX_ENV_STORE = 18;
}
```

#### Notification Message Registration

*   Function

    The client uses this interface to **register message types** it wants to receive from the blockchain (note: this feature is currently unavailable). Additionally, this is the **only interface** through which the **blockchain version information** can be retrieved.
*   Request Message Type

    `CHAIN_HELLO`
*   Request Data Object

    ```protobuf
    message ChainHello {
      repeated ChainMessageType api_list = 1; //By default, enable all apis
      int64   timestamp = 2;
    }
    ```
*   Request Parameter

    | Parameter | Type  | Description                                         |
    | --------- | ----- | --------------------------------------------------- |
    | api\_list | array | List of message types for applying for registration |
    | timestamp | int64 | Application time                                    |
*   Reponse Message Type

    `CHAIN_HELLO`
*   Reponse Data Object

    ```protobuf
    message ChainStatus {
      string self_addr        = 1;
      int64 ledger_version    = 2;
      int64 monitor_version   = 3;
      string buchain_version      = 4;
      int64   timestamp       = 5;
    }
    ```
*   Reponse Result

    | Variable         | Type   | Description            |
    | ---------------- | ------ | ---------------------- |
    | self\_addr       | string | Connected node address |
    | ledger\_version  | int64  | Ledger version         |
    | monitor\_version | int64  | Monitor version        |
    | zetrix\_version  | string | Zetrix version         |
    | timestamp        | int64  | Timestamp              |

#### Submit Transaction

*   Function

    The transaction to be executed is sent to the blockchain using a specific **WebSocket message type**. For full details on how to format the transaction, refer to the **Transaction Structure** documentation.
*   Request Message Type

    `CHAIN_SUBMITTRANSACTION`
*   Request Message Object

    ```protobuf
    //　Signature
    message Signature {
      string public_key = 1;
      bytes sign_data = 2;
    }

    //　Request object
    message TransactionEnv {
      Transaction transaction = 1;
      repeated Signature signatures   = 2;
      Trigger trigger = 3;
    }
    ```
*   Request Parameter

    | Parameter   | Type        | Description                                           |
    | ----------- | ----------- | ----------------------------------------------------- |
    | transaction | Transaction | Details for transaction structure。                    |
    | public\_key | string      | The public key of transaction sender                  |
    | sign\_data  | bytes       | Signature data obtained by signing `transaction_blob` |
* Reponse Message Type
  * `CHAIN_TX_STATUS`：This returns the **result of the transaction submission** note that a successful submission **only confirms receipt** by the blockchain, not successful execution.
  * `CHAIN_TX_ENV_STORE`：Returns the result of the transaction execution.
*   `CHAIN_TX_STATUS` Object

    ```protobuf
    message ChainTxStatus {
      enum TxStatus {
          UNDEFINED   = 0;
          CONFIRMED   = 1;    // web server will check tx parameters, signatures etc fist, noitfy CONFIRMED if pass
          PENDING     = 2;    // master will check futher before put it into pending queue
          COMPLETE    = 3;    // notify if Tx write ledger successfully
          FAILURE     = 4;    // notify once failed and set error_code
      };

      TxStatus    status = 1;
      string      tx_hash = 2;
      string      source_address = 3;
      int64       source_account_seq = 4;
      int64       ledger_seq = 5;         //on which block this tx records
      int64       new_account_seq = 6;        //new account sequence if COMPLETE
      ERRORCODE   error_code = 7;         //use it if FAIL
      string      error_desc = 8  ;           //error desc
      int64       timestamp = 9;          
    }
    ```
*   `ChainTxStatus` Member

    | Variable             | Type      | Description                                                   |
    | -------------------- | --------- | ------------------------------------------------------------- |
    | status               | TxStatus  | Transaction status.                                           |
    | tx\_hash             | string    | Transaction hash.                                             |
    | source\_address      | string    | Source account address of transaction sender.                 |
    | source\_account\_seq | int64     | Transaction sequence of transaction sender.                   |
    | ledger\_seq          | int64     | Height of the block where this transaction record is located. |
    | new\_account\_seq    | int64     | Block height when the transaction is completed.               |
    | error\_code          | ERRORCODE | Error code                                                    |
    | error\_desc          | string    | Error description                                             |
    | timestamp            | int64     | Timestamp                                                     |
*   `CHAIN_TX_ENV_STORE` Object

    ```protobuf
    message TransactionEnvStore{
      TransactionEnv transaction_env = 1;
      int32 error_code = 2;
      string error_desc = 3;
      int64 ledger_seq = 4;
      int64 close_time = 5;
      //for notify
      bytes hash = 6;
      int64 actual_fee = 7;
      repeated bytes contract_tx_hashes = 8;
    }
    ```
*   `TransactionEnvStore` Member

    | Variable             | Type           | Description                                                   |
    | -------------------- | -------------- | ------------------------------------------------------------- |
    | transaction\_env     | TransactionEnv | Submitted transaction content.                                |
    | error\_code          | int32          | Error code.                                                   |
    | error\_desc          | string         | Error description.                                            |
    | ledger\_seq          | int64          | Height of the block where this transaction record is located. |
    | close\_time          | int64          | Transaction completion time.                                  |
    | hash                 | bytes          | Transaction hash.                                             |
    | actual\_fee          | int64          | Actual transaction fee, uint is UGas                          |
    | contract\_tx\_hashes | bytes          | contract transaction hashes                                   |

#### Message Subscription

*   Function

    This interface implements transaction notifications that only specify the account address of the interface.
*   Request Message Type

    `CHAIN_SUBSCRIBE_TX`
*   Request Data Object

    ```protobuf
    message ChainSubscribeTx{
      repeated string address = 1;
    }
    ```
*   Request Parameter

    | Parameter | Type        | Description                      |
    | --------- | ----------- | -------------------------------- |
    | address   | Transaction | Account address for subscription |
*   Reponse Message Type

    `ChainResponse`
*   Reponse Data Object

    ```protobuf
    message ChainResponse{
          int32 error_code = 1;
          string error_desc = 2;
    }
    ```
*   Reponse Result

    | Variable    | Type   | Description       |
    | ----------- | ------ | ----------------- |
    | error\_code | int32  | Error code        |
    | error\_desc | string | Error description |

### Transaction structure

*   In protobuf format

    ```protobuf
    message Transaction {
        enum Limit{
            UNKNOWN = 0;
            OPERATIONS = 1000;
        };
        string source_address = 1;
        int64 nonce = 2;
        int64  fee_limit = 3;
        int64  gas_price =4;
        int64 ceil_ledger_seq = 5;
        bytes metadata = 6;
        repeated Operation operations = 7;
        int64 chain_id = 8;
    }
    ```
*   Keywords in protobuf

    | Keyword           | Type   | Description                                                                                                                                                                                                                                                                                                                                                                                   |
    | ----------------- | ------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
    | source\_address   | string | **Source Account**: The address of the transaction initiator. Upon successful transaction execution, the account's `nonce` will automatically increase by 1, representing the total number of transactions completed by that account.                                                                                                                                                         |
    | nonce             | int64  | <p>The <strong>nonce</strong> must equal the current nonce of the source account <strong>plus 1</strong>, ensuring each transaction is unique and preventing replay attacks.<br>To check an account's nonce, use the <strong><code>getAccount</code></strong> HTTP interface.<br>If the nonce is not shown in the response, the account’s current nonce is considered <strong>0</strong>.</p> |
    | fee\_limit        | int64  | <p></p><p><strong>Fee Limit</strong>: The maximum fee the transaction is allowed to consume.</p><ul><li>If the transaction <strong>succeeds</strong>, only the <strong>actual cost</strong> is charged.</li><li>If it <strong>fails</strong>, the full <strong>fee limit</strong> is deducted.<br><strong>Unit</strong>: UGas (1 Gas = 10⁸ UGas).</li></ul>                                   |
    | gas\_price        | int64  | <p><strong>Gas Price</strong>: Determines the handling fee per operation and contributes to the transaction's byte fee.<br><strong>Unit</strong>: UGas (1 Gas = 10⁸ UGas).</p>                                                                                                                                                                                                                |
    | ceil\_ledger\_seq | int64  | **Optional**: Sets a block height limit for when the transaction can be included. This is an advanced feature for controlling transaction validity.                                                                                                                                                                                                                                           |
    | operations        | array  | **Operation List**: Represents the core actions the transaction intends to perform. Refer to **Operations Structure** for detailed specifications.                                                                                                                                                                                                                                            |
    | metadata          | bytes  | **Optional**: A user-defined field that can be left empty or used to include a custom note.                                                                                                                                                                                                                                                                                                   |

### Operations structure

The corresponding `operations` in the protobuf structure of the transaction can contain one or more operations.

*   In protobuf format

    ```protobuf
    message Operation {
        enum Type {
            UNKNOWN = 0;
            CREATE_ACCOUNT          = 1;
            ISSUE_ASSET             = 2;
            PAY_ASSET               = 3;
            SET_METADATA            = 4;
            SET_SIGNER_WEIGHT       = 5;
            SET_THRESHOLD           = 6;
            PAY_COIN                = 7;
            LOG                     = 8;
            SET_PRIVILEGE           = 9;
        };
        Type type = 1;
        string source_address = 2;
        bytes metadata  = 3;

        OperationCreateAccount      create_account     = 4;
        OperationIssueAsset         issue_asset        = 5;
        OperationPayAsset           pay_asset          = 6;
        OperationSetMetadata        set_metadata       = 7;
        OperationSetSignerWeight    set_signer_weight  = 8;
        OperationSetThreshold       set_threshold      = 9;
        OperationPayCoin            pay_coin           = 10;
        OperationLog                log                = 11;
        OperationSetPrivilege       set_privilege      = 12;
    }
    ```
*   Keyword in protobuf

    | Keyword         | Type                   | Description                                                                                                                                                 |
    | --------------- | ---------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------- |
    | type            | Type                   | **Operation Code**: Specifies the type of operation to be executed. Each code corresponds to a distinct function. See **Operation Codes** for more details. |
    | source\_address | string                 | **Optional**: Indicates the operation's source account (the operator). If not specified, it defaults to the transaction's source account.                   |
    | metadata        | bytes                  | **Optional**: A custom field for user input, which can be left blank or used to add a note.                                                                 |
    | create\_account | OperationCreateAccount | Creating accounts operation                                                                                                                                 |
    | issue\_asset    | OperationIssueAsset    | Issuing assets operation                                                                                                                                    |
    | pay\_asset      | OperationPayAsset      | Transferring assets operation                                                                                                                               |
    | set\_metadata   | OperationSetMetadata   | Setting metadata operation                                                                                                                                  |
    | pay\_coin       | OperationPayCoin       | Transferring gas assets operation                                                                                                                           |
    | log             | OperationLog           | Recording logs operation                                                                                                                                    |
    | set\_privilege  | OperationSetPrivilege  | Setting privileges operation                                                                                                                                |

#### &#x20;<a href="#operation-codes" id="operation-codes"></a>

#### Operation Codes

| Operation Code | Description             |
| -------------- | ----------------------- |
| 1              | Creating Accounts       |
| 2              | Issuing Assets          |
| 3              | Transferring Assets     |
| 4              | Setting Metadata        |
| 7              | Transferring Gas Assets |
| 8              | Recording Logs          |
| 9              | Setting Privileges      |

#### Creating Accounts

The source account creates a new account on the blockchain. Creating Accounts are divided into Creating Normal Accounts and Creating Contract Accounts.

Protobuf format as follow:

```protobuf
// Key-Value pair
message KeyPair{
    string key = 1;
    string value = 2;
    int64 version = 3;
}

// Privilege
message Signer {
    enum Limit{
        SIGNER_NONE = 0;
        SIGNER = 100;
    };
    string address = 1;
    int64 weight = 2;
}
message OperationTypeThreshold{
    Operation.Type type = 1;
    int64 threshold = 2;
}
message AccountThreshold{
    int64 tx_threshold = 1; //required, [-1,MAX(INT64)] -1: indicates no setting
    repeated OperationTypeThreshold type_thresholds = 2;
}
message AccountPrivilege {
    int64 master_weight = 1;
    repeated Signer signers = 2;
    AccountThreshold thresholds = 3;
}

// Contract
message Contract{
    enum ContractType{
        JAVASCRIPT = 0;
    }
    ContractType type = 1;
    string payload = 2;
}

//　Create Account Operation
message OperationCreateAccount{
    string dest_address = 1;
    Contract contract = 2;
    AccountPrivilege priv = 3;
    repeated KeyPair metadatas = 4; 
    int64   init_balance = 5;
    string  init_input = 6;
}
```

**Creating Normal Accounts**

> **Note**: Both `master_weight` and `tx_threshold` must be 1 in the current operation. And only the following keywords are allowed to be initialized.

*   Keyword in protobuf

    | Keyword        | Type   | Description                                                                                                                 |
    | -------------- | ------ | --------------------------------------------------------------------------------------------------------------------------- |
    | dest\_address  | string | Address of the target account. When creating a normal account, it cannot be empty                                           |
    | init\_balance  | int64  | Initial Gas value of the target account, in UGas, 1 Gas = 10^8 UGas                                                         |
    | master\_weight | int64  | Master weight of the target account, which ranges \[0, MAX(UINT32)]                                                         |
    | tx\_threshold  | int64  | The threshold for initiating a transaction below which the transaction cannot be initiated, which ranges ​​\[0, MAX(INT64)] |
*   Query

    The account information is queried through the getAccount interface in HTTP.



**Creating Contract Accounts**

> **Note**: In the current operation, `master_weight` must be 0 and `tx_threshold` must be 1. And only the following keywords are allowed to be initialized

*   Keyword in protobuf

    | Keyword        | Type   | Description                                                                                                    |
    | -------------- | ------ | -------------------------------------------------------------------------------------------------------------- |
    | payload        | string | Contract code                                                                                                  |
    | init\_balance  | int64  | Initial Gas value of the target account, in UGas, 1 Gas = 10^8 UGas                                            |
    | init\_input    | string | **Optional**: Provides input parameters for the `init` function in the contract code during contract creation. |
    | master\_weight | int64  | Master weight of the target account                                                                            |
    | tx\_threshold  | int64  | The threshold for initiating a transaction below which it is not possible to initiate a transaction.           |
* Query
  * The account information is queried through the getAccount interface in HTTP.
  *   Query with the getTransactionHistory interface in HTTP, and the result is as follows:

      ```protobuf
      [
          {
              "contract_address": "ztxSn8xpL7c2xkxwbreVCs6EZ7KZbBvtDaLtV", //The contract account
              "operation_index": 0                                        //The operation index value in the transaction array, 0 means the first transaction
          }
      ]
      ```

#### &#x20;<a href="#issuing-assets" id="issuing-assets"></a>

#### Issuing Assets

*   Function

    The **source account** in this operation issues a digital asset. Upon successful execution, the asset is added to the source account’s asset balance.
*   In protobuf format

    ```protobuf
    message OperationIssueAsset{
        string code = 1;
        int64 amount = 2;
    }
    ```
*   Keyword in protobuf

    | Keyword | Type   | Description                                                    |
    | ------- | ------ | -------------------------------------------------------------- |
    | code    | string | Code of the asset to be issued, which ranges \[1, 64]          |
    | amount  | int64  | Amount of the asset to be issued, which ranges (0, MAX(int64)) |

#### Transferring Assets

> **Note**: If the target account is a contract account, the current operation triggers the contract execution of the target account.

*   Function

    The source account of this operation transfers an asset to the target account.
*   In protobuf format

    ```protobuf
    message AssetKey{
         string issuer = 1;
         string code = 2;
         int32 type = 3;
    }
    message Asset{
         AssetKey   key = 1;
         int64      amount = 2;
    }

    //　Pay asset operation
    message OperationPayAsset{
        string dest_address = 1;
        Asset asset = 2;
        string input = 3;
    }
    ```
*   Keyword in protobuf

    | Keyword       | Type   | Description                                                                                                                                                                     |
    | ------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
    | dest\_address | string | Address of the target account                                                                                                                                                   |
    | issuer        | string | Address of the issuer                                                                                                                                                           |
    | code          | string | Asset code which ranges \[1, 64]                                                                                                                                                |
    | amount        | int64  | The amount of the asset which ranges (0,MAX(int64))                                                                                                                             |
    | input         | string | **Optional**: If the target account is a contract, the input is passed as an argument to the `main` function of the contract code. This setting is ignored for normal accounts. |

#### Setting Metadata

*   Function

    The source account of this operation modifies or adds metadata to the metadata table.
*   In protobuf format

    ```protobuf
    message OperationSetMetadata{
        string  key = 1;  
        string  value = 2;
        int64   version = 3;
        bool    delete_flag = 4;
    }
    ```
*   Keyword in protobuf

    | Keyword      | Type   | Description                                                                                                                                                                                                                                                |
    | ------------ | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
    | key          | string | Keyword of metadata, which ranges (0, 1024].                                                                                                                                                                                                               |
    | value        | string | Content of metadata, which ranges \[0, 256K].                                                                                                                                                                                                              |
    | version      | int64  | <p></p><p><strong>Optional</strong>: Metadata version number, default is <code>0</code>.</p><ul><li><code>0</code>: No version restriction</li><li><code>>0</code>: Must match this specific version</li><li><code>&#x3C;0</code>: Invalid value</li></ul> |
    | delete\_flag | bool   | Whether to delete the metadata.                                                                                                                                                                                                                            |

#### Setting Privileges

*   Function

    Set the weights that the signer has and set the thresholds required for each operation. For details, see Assignment of Control Rights in HTTP.
*   In protobuf format

    ```protobuf
    message Signer {
        enum Limit{
            SIGNER_NONE = 0;
            SIGNER = 100;
        };
        string address = 1;
        int64 weight = 2;
    }
    message OperationTypeThreshold{
        Operation.Type type = 1;
        int64 threshold = 2;
    }

    //　Set privilege object
    message OperationSetPrivilege{
        string master_weight = 1;
        repeated Signer signers = 2;
        string tx_threshold = 3;
        repeated OperationTypeThreshold type_thresholds = 4;
    }
    ```
*   Keywords in protobuf

    | Keyword          | Type   | Description                                                                                                                                                                                                                                                                                                                                                         |
    | ---------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
    | master\_weight   | string | <p><strong>Optional</strong>: Defaults to <code>""</code>, representing the account’s master weight.</p><ul><li><code>""</code>: No change</li><li><code>"0"</code>: Set master weight to 0</li><li><code>"1"</code> to <code>"MAX(UINT32)"</code>: Assign specified weight</li><li>Others: Invalid input</li></ul>                                                 |
    | signers          | array  | **Optional**: A list of signers required for the operation. Defaults to an empty object, which means no signers are configured.                                                                                                                                                                                                                                     |
    | address          | string | **Signer Address**: The address of the signer to be configured, which must comply with Zetrix’s address verification rules.                                                                                                                                                                                                                                         |
    | weight           | int64  | <p><strong>Optional</strong>: Defaults to <code>0</code>.</p><ul><li><code>0</code>: Removes the signer</li><li><code>(0, MAX(UINT32)]</code>: Sets the signer's weight</li><li>Others: Invalid input</li></ul>                                                                                                                                                     |
    | tx\_threshold    | string | <p><strong>Optional</strong>: Defaults to <code>""</code>, indicating the minimum privilege (<code>tx_threshold</code>) for the account.</p><ul><li><code>""</code>: Do not modify</li><li><code>"0"</code>: Set <code>tx_threshold</code> to 0</li><li><code>"1"</code> to <code>"MAX(INT64)"</code>: Set specified weight</li><li>Others: Invalid input</li></ul> |
    | type\_thresholds | array  | **Optional**: A list of required thresholds for different operations. Defaults to an empty object, meaning no thresholds are configured.                                                                                                                                                                                                                            |
    | type             | int    | Indicates a specific **operation type** within the valid range of **(0, 100]**.                                                                                                                                                                                                                                                                                     |
    | threshold        | int64  | <p><strong>Optional</strong>: Defaults to <code>0</code>.</p><ul><li><code>0</code>: Removes the specified operation type</li><li><code>(0, MAX(INT64)]</code>: Sets the weight for the operation</li><li>Other values: Invalid input</li></ul>                                                                                                                     |

#### &#x20;<a href="#transferring-gas-assets" id="transferring-gas-assets"></a>

#### Transferring Gas Assets

> **Note**: If the **target account** is a **contract account**, this operation will **trigger the execution** of the contract code within that account.

*   Function

    Two functions:

    1. The source account of this operation transfers a Gas asset to the target account.
    2. The source account of this operation creates a new account on the blockchain.
*   In protobuf format

    ```protobuf
    message OperationPayCoin{
        string dest_address = 1;
        int64 amount = 2;
        string input = 3;
    }
    ```
*   protobufKeyword

    | Keyword       | Type   | Description                                                                                                                                                      |
    | ------------- | ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------------- |
    | dest\_address | string | Receiver account                                                                                                                                                 |
    | amount        | array  | **Optional**: A list of signers required for the operation. Defaults to an empty object, meaning no signers are specified.                                       |
    | input         | string | **Optional**: If the target is a contract account, the input is passed to the `main` function of the contract code. This setting is ignored for normal accounts. |

#### Recording Logs

*   Function

    The source account of this operation writes the log to the blockchain.
*   In protobuf format

    ```protobuf
    message OperationLog{
        string topic = 1;
        repeated string datas = 2;
    }
    ```
*   protobufKeyword

    | Keyword | Type   | Description                                                                         |
    | ------- | ------ | ----------------------------------------------------------------------------------- |
    | topic   | string | The **log topic** and **parameter length** must fall within the range **(0, 128]**. |
    | datas   | array  | **Log Content**: Each element must have a length within the range **(0, 1024]**.    |

### Error Codes

The error code is composed of two parts:

* error\_code : Error code, approximate error classification
* error\_desc : Error Description, which can accurately find the error specific information from the error description

The error list is as follows:

| Error code | Name                                        | Description                                                                                                                                        |
| ---------- | ------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------- |
| 0          | ERRCODE\_SUCCESS                            | Successful operation                                                                                                                               |
| 1          | ERRCODE\_INTERNAL\_ERROR                    | Inner service defect                                                                                                                               |
| 2          | ERRCODE\_INVALID\_PARAMETER                 | Parameters error                                                                                                                                   |
| 3          | ERRCODE\_ALRDY\_EXIST                       | Objects already exist, such as repeated transactions submitted                                                                                     |
| 4          | ERRCODE\_NOT\_EXIST                         | Objects do not exist, such as null account, transactions and blocks etc                                                                            |
| 5          | ERRCODE\_TX\_TIMEOUT                        | Transactions expired. It means the transaction has been removed from the buffer, but it still has probability to be executed                       |
| 7          | ERRCODE\_MATH\_OVERFLOW                     | Math calculation is overflown                                                                                                                      |
| 20         | ERRCODE\_EXPR\_CONDITION\_RESULT\_FALSE     | The expression returns false. It means the TX failed to be executed currently, but it still has probability to be executed in the following blocks |
| 21         | ERRCODE\_EXPR\_CONDITION\_SYNTAX\_ERROR     | The syntax of the expression returns is false. It means that the TX must fail                                                                      |
| 90         | ERRCODE\_INVALID\_PUBKEY                    | Invalid public key                                                                                                                                 |
| 91         | ERRCODE\_INVALID\_PRIKEY                    | Invalid private key                                                                                                                                |
| 92         | ERRCODE\_ASSET\_INVALID                     | Invalid assets                                                                                                                                     |
| 93         | ERRCODE\_INVALID\_SIGNATURE                 | The weight of the signature does not match the threshold of the operation                                                                          |
| 94         | ERRCODE\_INVALID\_ADDRESS                   | Invalid address                                                                                                                                    |
| 97         | ERRCODE\_MISSING\_OPERATIONS                | Absent operation of TX                                                                                                                             |
| 98         | ERRCODE\_TOO\_MANY\_OPERATIONS              | Over 100 operations in a single transaction                                                                                                        |
| 99         | ERRCODE\_BAD\_SEQUENCE                      | Invalid sequence or nonce of TX                                                                                                                    |
| 100        | ERRCODE\_ACCOUNT\_LOW\_RESERVE              | Low reserve in the account                                                                                                                         |
| 101        | ERRCODE\_ACCOUNT\_SOURCEDEST\_EQUAL         | Sender and receiver accounts are the same                                                                                                          |
| 102        | ERRCODE\_ACCOUNT\_DEST\_EXIST               | The target account already exists                                                                                                                  |
| 103        | ERRCODE\_ACCOUNT\_NOT\_EXIST                | Accounts do not exist                                                                                                                              |
| 104        | ERRCODE\_ACCOUNT\_ASSET\_LOW\_RESERVE       | Low reserve in the account                                                                                                                         |
| 105        | ERRCODE\_ACCOUNT\_ASSET\_AMOUNT\_TOO\_LARGE | Amount of assets exceeds the limitation ( int64 )                                                                                                  |
| 106        | ERRCODE\_ACCOUNT\_INIT\_LOW\_RESERVE        | Insufficient initial reserve for account creation                                                                                                  |
| 111        | ERRCODE\_FEE\_NOT\_ENOUGH                   | Low transaction fee                                                                                                                                |
| 114        | ERRCODE\_OUT\_OF\_TXCACHE                   | TX buffer is full                                                                                                                                  |
| 120        | ERRCODE\_WEIGHT\_NOT\_VALID                 | Invalid weight                                                                                                                                     |
| 121        | ERRCODE\_THRESHOLD\_NOT\_VALID              | Invalid threshold                                                                                                                                  |
| 144        | ERRCODE\_INVALID\_DATAVERSION               | Invalid data version of metadata                                                                                                                   |
| 146        | ERRCODE\_TX\_SIZE\_TOO\_BIG                 | TX exceeds upper limitation                                                                                                                        |
| 151        | ERRCODE\_CONTRACT\_EXECUTE\_FAIL            | Failure in contract execution                                                                                                                      |
| 152        | ERRCODE\_CONTRACT\_SYNTAX\_ERROR            | Failure in syntax analysis                                                                                                                         |
| 153        | ERRCODE\_CONTRACT\_TOO\_MANY\_RECURSION     | The depth of contract recursion exceeds upper limitation                                                                                           |
| 154        | ERRCODE\_CONTRACT\_TOO\_MANY\_TRANSACTIONS  | the TX submitted from the contract exceeds upper limitation                                                                                        |
| 155        | ERRCODE\_CONTRACT\_EXECUTE\_EXPIRED         | Contract expired                                                                                                                                   |
| 160        | ERRCODE\_TX\_INSERT\_QUEUE\_FAIL            | Failed to insert the TX into buffer                                                                                                                |
