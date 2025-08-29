# Common Problems

### Whether the node is running normally after the deployment is complete

After the deployment is complete, check whether the block height increases normally. If the block height is greater than 1, it means that the node is running normally.

*   Command

    Calling the following command（For example, the IP is 127.0.0.1 and the port is 18002.）：

    ```shell
    [root@zetrix ~]# curl 127.0.0.1:18002/getLedger
    ```
*   Result

    The results are as follows:

    ```json
    {
      "error_code" : 0,
      "result" : {
        "header" : {
          "account_tree_hash" : "bf337b72bb5ab150f25a4e665259049cd94fa70966a1c0f56f79a44969980ccb",
          "close_time" : 1558595960522453,
          "consensus_value_hash" : "04c172793d72b14ce2da8c5a9f9b7366edf75bc3c81aaf9f3069e6af3af1c857",
          "fees_hash" : "916daa78d264b3e2d9cff8aac84c943a834f49a62b7354d4fa228dab65515313",
          "hash" : "7349292089a68b134c03aefceed8a3ce0bf69960a21a6ca41467a672d3e2c3ce",
          "previous_hash" : "5d86cc2bb4a97831c4f8bbb1bbb8a09289337c42c33fa64bc7c1aa352b17b2ba",
          "seq" : 3,
          "validators_hash" : "9ff25c4231deb81c44eec379fd2467156d2389c5d69edf308d38f7b5ac53705b",
          "version" : 1002
        }
      }
    }
    ```

    > **Note**: Here seq is the block height, it is 3, and it continues to grow, indicating that the node is running normally.

### The node is running normally, but the transaction commit always fails

Check to see if the node is synchronized. For nodes that are not synchronized, the sent transaction cannot be executed normally.

*   Command

    View the current node status, the command is as follows:

    ```shell
    [root@zetrix ~]# curl 127.0.0.1:18002/getModulesStatus
    ```
*   Result

    The partial results are shown below:

    ```shell
    ...
    "ledger_manager" : {
          "account_count" : 117,
          "chain_max_ledger_seq" : 3185646,
          "hash_type" : "sha256",
          "ledger_context" : {
             "completed_size" : 0,
             "running_size" : 0
          },
          "ledger_sequence" : 65017,
    },
    ...
    ```

    > **Note**: The `chain_max_ledger_seq` represents the latest block height of the entire Zetrix blockchain, while the `ledger_sequence` reflects the current block height of the local node. If `ledger_sequence` is lower than `chain_max_ledger_seq`, it indicates that the node is not fully synchronized with the network. As a result, transactions submitted through this node may not be executed correctly until synchronization is complete.
