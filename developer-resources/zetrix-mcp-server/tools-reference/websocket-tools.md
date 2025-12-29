# WebSocket Tools

Real-time blockchain monitoring via persistent WebSocket connections.

### Establish WebSocket Connection(**`zetrix_ws_connect`)**

Connect and register to WebSocket for real-time updates.

**Purpose:** Establish persistent bidirectional connection to Zetrix node.

**Returns:**

* Connection status
* Blockchain version
* Node information

**Connection Details:**

* Mainnet: `ws://node-ws.zetrix.com`
* Testnet: `ws://test-node-ws.zetrix.com`

**Automatic Handling:**

* Reconnects on disconnect
* Queues messages during reconnection

***

### Submit Transaction via WebSocket(**`zetrix_ws_submit_transaction`)**

Submit transaction via WebSocket with real-time status.

**Purpose:** Send transaction and receive instant status updates as it processes.

**Advantages over HTTP:**

* Real-time status updates
* Instant confirmation notifications
* No polling required

**Timeout:** 30 seconds for confirmation, then falls back to polling.

***

### Subscribe to Transaction Events(**`zetrix_ws_subscribe_tx`)**

Subscribe to transaction notifications for addresses.

**Purpose:** Monitor specific addresses and get notified of any transactions.

**Parameters:**

* `addresses` (array): List of addresses to monitor

**Notifications:**

* Incoming transactions
* Outgoing transactions
* Transaction details

**Limits:** Maximum 100 subscriptions per connection

**Use Cases:**

* Payment processing
* Wallet notifications
* Exchange deposit monitoring

***

### Subscribe to Transaction Events(**`zetrix_ws_disconnect`)**

Disconnect from WebSocket.

**Purpose:** Properly close WebSocket connection and clean up resources.

**Note:** Can reconnect after disconnect by calling `ws_connect` again.

***

### Retrieve WebSocket Connection Status(**`zetrix_ws_status`)**

Check WebSocket connection status.

**Purpose:** Verify connection state and health.

**Returns:**

* Connection state (connected/disconnected)
* Message count
* Errors
* Reconnection attempts
* Latency

***
