# Maintenance

### Starting the Zetrix Service

Input the following command to start the zetrix service.

```shell
./ztx start
```

> **Note**: You must enter the /usr/local/ztxchain/scripts directory to execute ztx command.

### Stopping the Zetrix Service

Input the following command to stop the zetrix service.

```shell
./ztx stop
```

### Querying the Zetrix Service Status

Input the following command to query the zetrix service.

```shell
./ztx status
```

### Querying the Detailed System Status

Input the following command to query the detailed system status.

```shell
curl 127.0.0.1:18002/getModulesStatus
```

The result is shown below:

```json
{
    "glue_manager":{
        "cache_topic_size":0,
        "ledger_upgrade":{
            "current_states":null,
            "local_state":null
        },
        "system":{
            "current_time":"2017-07-20 10:32:22", //Current system time
            "process_uptime":"2017-07-20 09:35:06", //When zetrix is started
            "uptime":"2017-05-14 23:51:04"
        },
        "time":"0 ms",
        "transaction_size":0
    },
    "keyvalue_db":Object{...},
    "ledger_db":Object{...},
    "ledger_manager":{
        "account_count":2316,  //Total accounts
        "hash_type":"sha256",
        "ledger_sequence":12187,
        "time":"0 ms",
        "tx_count":1185   //Total transactions
    },
    "peer_manager":Object{...},
    "web server":Object{...}
}
```



### Clearing Database

Before clearing the database, you must stop the Zetrix service. Follow these steps to properly clear the database:

1.  Input the following command to enter the zetrix service directory.

    ```shell
    cd /usr/local/ztxchain/bin
    ```
2.  Input the following command to clear the database.

    ```shell
    ./ztx --dropdb
    ```

> **Note**: After the database is successfully cleared, you can see the information shown below.

![](https://docs.bubi.cn/en/docs/assets/clear_database.png)

### Creating a Hard Fork

You must complete the following steps to create a hard fork.

1.  Create the hard fork by inputting the following command in the /usr/local directory.

    ```shell
    ./ztxchain/bin/ztx --create-hardfork
    ```
2. Enter `y` when prompted and then press `Enter`. The message shown below indicates the hard fork is created successfully.

![](https://docs.bubi.cn/en/docs/assets/hard_fork_created.png)

> **Note**:
>
> * After executing the above command, the new blockchain network has only one verification node.
> * After executing the hard fork command, the following hash value is displayed:

```shell
    4b9ad78065c65aaf1280edf6129ab2da93c99c42f2bcd380b5966750ccd5d80d
```

1.  Input the following command to clear the consensus status data. When clearing the consensus status data, you must ensure that the zetrix service is not running, otherwise it cannot be cleared.

    ```shell
    ./ztxchain/bin/ztx --clear-consensus-status
    ```
2.  Add the hash value to the ztx.json file in the /usr/local/ztxchain/config directory of the node or synchronization node.

    ```json
    "ledger": {
        "genesis_account": "ztxSe9NGrGXmNBhwDerRyRyaXouFT4WbPT7LA",
        "max_trans_per_ledger": 1000,
        "hardfork_points":[
            "4b9ad78065c65aaf1280edf6129ab2da93c99c42f2bcd380b5966750ccd5d80d"
        ]
    },
    ```
3. Start the node service for the configuration to take effect.

### Updating the configure file

Before updating the configure file, you must make sure that the Zetrix service is down. If you want to update the configure file of the Zetrix node, you can modify it by following the steps below.

1.  Input the following command to enter the directory where the configuration file is located.

    ```shell
    cd /usr/local/ztxchain/config/
    ```
2.  Change the configuration file name (ztx.json) for the current running environment, for example:

    ```shell
    cp ztx.json ztx-previous.json
    ```
3.  Update the configure file ztx.json, for example:

    ```shell
    vi ztx.json
    ```

> **Note**:
>
> * If you want to update the configure file, you can refer to xxx
> * After changing the running environment, you must clear the database to restart the zetrix service.
