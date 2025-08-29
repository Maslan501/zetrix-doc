# Dedicated Address

### Deploy Sync Node With Dedicated P2P Address

To enable this, initially we need to create our own P2P Zetrix address. We can use our Zetrix wallet to generate the Zetrix address. Alternatively, we can use the "ztx" binary to generate the address by running the following command.

```
cd /usr/local/ztxchain/bin && ./ztx --create-account
```

```
{
   "address" : "ZTX3QCcugyyMMpn9iXmkBbkLyip5HSaiqrjsS",
   "private_key" : "privBwvertKepm1su9fUVUurSDCUvNcKtm5Csah8iYQfrXV1mdJVvMMB",
   "private_key_aes" : "9679f6976f7dcb6360e679a8e34fed89ba293190920e3d37f6acb304a470a23d4ae4a5a81d68c0c41f9e85056071d30a26a0511b5c825aa8c14caea301c2350b",
   "private_key_raw" : "9cdec3bd43e7195e2ce487e120ed1967abc2958a2f6e749bc770002891f2eca7",
   "public_key" : "b0016dfbca59a3ca487f084c957de666439c4a030ddb991e3f54bff4b66ff735a7b1c096ce68",
   "public_key_raw" : "6dfbca59a3ca487f084c957de666439c4a030ddb991e3f54bff4b66ff735a7b1",
   "sign_type" : "ed25519"
}
```

Now we are able to replace our P2P address with the newly generated address. To replace, navigate to the config file (ztx.json), open it using vim or nano editor.

```
vi /usr/local/ztxchain/config/ztx.json
```

Change the following P2P and validator section. Copy the "address" to "p2p.address" and "private\_key\_aes" to "p2p.node\_private\_key" attribute in the json file.

Mainnet:

<pre><code>"p2p": {
    "network_id": 5687162,
    "address": "ZTX3QCcugyyMMpn9iXmkBbkLyip5HSaiqrjsS",
    "node_private_key": "9679f6976f7dcb6360e679a8e34fed89ba293190920e3d37f6acb304a470a23d4ae4a5a81d68c0c41f9e85056071d30a26a0511b5c825aa8c14caea301c2350b",
    "consensus_network": {
        "heartbeat_interval": 60,
        "listen_port": 18001,
        "target_peer_connection": 50,
        "max_connection": 20000,
        "connect_timeout": 5,
                    "known_peers":[
                            "ws-node.zetrix.com:80"
                    ]
    }
},
,
,
,
<strong>"genesis": {
</strong>    "chain_id": 0,
    "account": "ZTX3ZmEr5huWA5n6S8NXnN8SQUjiJKG3aKceM",
    "slogan": "Cross chain,Cross border",
<strong>    "fees": {
</strong><strong>          "base_reserve": 0,
</strong>          "gas_price": 100
     },
    "validators":[
<strong>          "ZTX3ceE5v6k78UFNZvUeYdubJhaJvb5zKwte7",
</strong><strong>          "ZTX3XPZUbxrSddZxCzn7yhXq2sVg5wiphY8KQ",
</strong><strong>          "ZTX3TVVTeWzCzNemCxWZ6GLTf7zG1CUw637E3",
</strong>          "ZTX3HZKgT5yPT56afuCyLFMNWg2mkAXBXj2QQ",
          "ZTX3QnQ3NscC9c9zBbSs6Th6a7AUb5j6tjfoh",
          "ZTX3c8nSKgU6cXQXFetfX4FKJKmjetkDQogA4",
          "ZTX3GL4Je47T8H4MHkRdzT8YLPVg3Baphhark",
          "ZTX3Hx6nTL7qPsR9cD2DGxdShRQFX6T3X5oub",
          "ZTX3YXmFR3jP62vbP3jAUL8KndbT2hLkJBzPx",
          "ZTX3PwCoMC64cYRbm35k4RRWDSytVz4jBuhZQ",
          "ZTX3Jk3NtVZ7ZuTT1znGwS6t23Fa7bkU9iE83",
          "ZTX3MNyGpbuHzJXzHFKxbJkuNxiRvBaFALJ6r",
          "ZTX3H7Vmj5D71f2WPQpEH7NbZ6ivZjAus4QwB",
          "ZTX3UybPuVrj9Aw2HNwNNZYCQDk9TPovtdgBf",
          "ZTX3dQh7o3B9KtkufQnK6gEQT3eQDXQo13tN3",
          "ZTX3KyKnRefindtK1Fx7m8u6xgARw64bzaYkv",
          "ZTX3WtPuXeJPj6GNhQgXZCdx6wVciCoYLViLN",
          "ZTX3NEREphask9uasokfSo1sniuSetH6KUroT",
          "ZTX3TKPnA8B6YoSeYG85tzgK1kcsKsy8KSdKH",
          "ZTX3KxJYhYRBnETzQAq6GVqKwLD1iNY5vMk5H"
   ]
},
</code></pre>

Testnet:

<pre><code>"p2p": {
    "network_id": 1635227727,
    "ssl": {
        "chain_file": "config/node_ztx.crt",
        "private_key_file": "config/node_ztx.pem",
        "private_password": "67e2a4fa7e39c55f9e7b1234ccba0827",
        "dhparam_file": "config/dh2048.pem",
        "verify_file": "config/ca.crt"
        },
    "consensus_network": {
        "heartbeat_interval": 60,
        "listen_port": 9333,
        "target_peer_connection": 50,
        "max_connection": 20000,
        "connect_timeout": 5,
		"known_peers":[
			"test-seed1-node-ws.zetrix.com:80"
		]
    }
},
,
,
,
"genesis": {
    "chain_id": 0,
    "account": "ZTX3WqX9oX6v9TgNT8syfidKpzDv5veh14L4s",
    "slogan": "a new era of value",
    "fees": {
        "base_reserve": 0,
        "gas_price": 100
    },
    "validators":[
            "ZTX3a3Dk3ges1AszHQzEDfURtpH7mQPPN2jdk",
            "ZTX3U1siUEjKwDCYBhiGk7A9S2mSzskT4Gn8H",
            "ZTX3U27gSSFPR1ggoGfXe5X5rbQCV9CvHafJH",
            "ZTX3NC7ETj6K7dPNpv73oGgrqU1mxvyRyyWQN"
<strong>    ]
</strong>},


</code></pre>

After replacing the address, you may restart the node by running the following command.

```
cd /usr/local/ztxchain/scripts && ./ztx restart
```

Check the node status again to ensure the node is running.

```
./ztx status
```

### Deploy Validator Node With Dedicated Validator Address

In order to set up a validator node, we need to manually replace the validator address and private key before registering our node and stake at [https://validator.zetrix.com/](https://validator.zetrix.com/).

To set the validator address and private key, follow the same method as above to generate a new address. Copy the "address" to "ledger.validation\_address" and "private\_key\_aes" to "ledger.validation\_private\_key" attribute in the configuration json file (ztx.json).

```
"ledger": {
    "validation_address": "ZTX3RvGfvmNv9k5UQdZo4PfRrjSMh1wckW2nq",
    "validation_private_key": "cf17b54fda991ef1968620111049fa959752ab721667a6f455ed385dc964210ec9064d69e6aa2c79d982dc6bbaf22e3ca2efa8704068abe10f0887a99a2c8792",
    "max_trans_per_ledger": 1000,
    "hash_type": 0,
    "hardfork_points": [],
    "tx_pool":{
        "queue_limit":10240,
        "queue_per_account_txs_limit":64
    }
},
```

Then, you may rerun the node using the restart command as shown above.
