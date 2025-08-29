# Node Installation

## Node Binary Installation

Download the node .tar.gz file from the following link.

For mainnet:

```bash
wget https://github.com/Zetrix-Chain/zetrix/releases/download/1.0.2/mainnet_1.0.2_linux_amd64.tar.gz
```

For testnet:

```bash
wget https://github.com/Zetrix-Chain/zetrix/releases/download/1.0.2/testnet_1.0.2_linux_amd64.tar.gz
```

You can checkout our latest release [here](https://github.com/Zetrix-Chain/zetrix/releases), where you can also get .zip packages.

```
tar -xzf mainnet_1.0.2_linux_amd64.tar.gz -d /usr/local/
```

After extracting the file, a new folder named "ztxchain" will appear at /usr/local directory. The folder contains few other sub folders.

<table><thead><tr><th>Folder</th><th>Description</th><th data-hidden>Description</th><th data-hidden></th></tr></thead><tbody><tr><td>bin</td><td>Node executable binary (ztx and ztxd)</td><td>Binary executable file (ztx, ztxd) to run the node</td><td></td></tr><tr><td>config</td><td>Configuration file for the node (ztx.json)</td><td>Configuration file (ztx.json) for the node</td><td></td></tr><tr><td>coredump</td><td>Crash debugging log</td><td>Crash debugging log</td><td></td></tr><tr><td>data</td><td>Node storage database location</td><td>Node database location</td><td></td></tr><tr><td>jslib</td><td>Extra library </td><td>Library used for the execution</td><td></td></tr><tr><td>log</td><td>Running and error logs</td><td>Executioun</td><td></td></tr><tr><td>scripts</td><td>Script to run executable binary as service</td><td></td><td></td></tr><tr><td></td><td></td><td></td><td></td></tr></tbody></table>

### Deploy Sync Node With Automated P2P Address Generation

Navigate to the "scripts" folder.

```
cd /usr/local/ztxchain/scripts
```

To run the node, execute the "ztx" script as shown below.

<pre><code><strong>./ztx start
</strong></code></pre>

Check the node status, the status should display the node is running.

```
./ztx status
```

Make sure to open P2P port (18001), API port (18002) and Websocket port (18003). The ports can be changed from configuration file (ztx.json).&#x20;

If you deploy the node from within your home network router, please make sure to do port forwarding on the router to allow inbound connection for these ports.
