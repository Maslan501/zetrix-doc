# Docker Deployment

### Node Deployment Via Docker

The docker image can be used to simplify the installation process in various platforms. Click [here](https://hub.docker.com/layers/zetrixchain/zetrix-testnet/1.0.2/images/sha256-a665760c9535857a0ac2a8ffd2a33a17e7a2824ba7b0041d9166a036dae94c35?context=repo) for the testnet docker image and [here ](https://hub.docker.com/layers/zetrixchain/zetrix-mainnet/1.0.2/images/sha256-b8a86ffdf636f576023479ced9b8d57a3771495e25c7f9ea3b8640b4f9c0f329?context=repo)for the mainnet docker image.&#x20;

Before deploying using docker, we need to make sure the docker has been installed properly in our operating system.

#### Linux Environment

To run the docker container in Linux environment, execute the following command.

```
docker run -d --name zetrix-mainnet --network host zetrixchain/zetrix-mainnet
```

#### Window / MAC Environment

```
docker run -d -p 18001:18001 -p 18002:18002 -p 18003:18003--name zetrix-mainnet zetrixchain/zetrix-mainnet
```

After running the docker container, we can check our node status by running this command via command line.&#x20;

```
curl localhost:18002/getLedger
```

It should display the information of the node.

```
{
   "error_code" : 0,
   "result" : {
      "header" : {
         "account_tree_hash" : "33ce2ffa4c16adf9cc3041e80a1d77453a8a8c04b31d1a24821330363acf9372",
         "close_time" : 1665640579169377,
         "consensus_value_hash" : "8687aec6e653f9abcf0c8764e6978a9af89382e11da175e382cb44c90f56a890",
         "fees_hash" : "c745bff5807b8395268c224c9c6c3cc4916369c0ff03ef0b8c015efbb3f054f2",
         "hash" : "882e5b4a776e0c760f7ebbf4bba17af070de9416f45d7e788cd8e3459b310902",
         "previous_hash" : "695beb752f74db9cc3500ab2e5b3c14cb265b4e8c267258a916478999d24b0be",
         "seq" : 621387,
         "tx_count" : 668698,
         "validators_hash" : "d27d944b9eb7da9266fac6634f8001138f3545389705957036580fb26e7630e1",
         "version" : 1000
      },
      "ledger_length" : 227
   }
}
```
