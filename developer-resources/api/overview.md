# Overview

### Overview of Zetrix API

Zetrix offers a comprehensive set of **API interfaces** including **HTTP** and **WebSocket** to simplify user interaction with the blockchain. These APIs allow developers to efficiently query data, submit transactions, and receive real-time updates, streamlining integration and application development on the Zetrix network.

#### HTTP Interface Overview

​The request data and response data in the HTTP interface are in JSON format.

The configuration items of the HTTP interface are configured under the `webserver` file in the `config` directory. The configuration contents are as follows:

```json
"webserver": {
  "listen_addresses": "0.0.0.0:36002" // Listen IP and PORT
}
```

#### Websocket Interface Overview

The WebSocket interface in Zetrix uses **Protocol Buffer 3 (proto3)** for both request and response data, a data serialization format developed by Google; users unfamiliar with it are encouraged to refer to the proto3 documentation for more details.

​The configuration items of the Websocket interface are configured under the `wsserver` file in the `config` directory. The configuration contents are as follows:

```json
"wsserver": {
    "listen_address": "0.0.0.0:36003" // Listen IP and PORT
}
```

### Transaction Process

1. **Assemble the Transaction Object**
   * Create the transaction according to your requirements.
   * Use JSON format for HTTP interfaces and Protobuf format for WebSocket interfaces.
2. **Serialize the Transaction**
   * Convert the transaction object into a byte stream (`transaction_blob`).
   * Use `getTransactionBlob` for HTTP or the Protobuf `serialize` method for WebSocket.
3. **Sign the Transaction**
   * Sign the `transaction_blob` with your private key (`skey`) to generate `sign_data`.
   * The corresponding public key (`pkey`) will also be used.
4. **Submit the Transaction**
   * Use the appropriate submit method: HTTP Submit Transaction or WebSocket Submit Transaction.
5. **Verify Transaction Execution**
   * For HTTP: use `getTransactionHistory`.
   * For WebSocket: check the response message from the submitted transaction.
