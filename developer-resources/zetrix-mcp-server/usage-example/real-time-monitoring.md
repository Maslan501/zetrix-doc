# Real-Time Monitoring

### **Connect to WebSocket**

**You:**

```
Connect to the Zetrix WebSocket so we can monitor real-time events
```

**Claude will:**

* Use `zetrix_ws_connect`
* Establish connection and register for events

**Expected Response:**

```
Connected to Zetrix WebSocket!
- Node: ws://node-ws.zetrix.com
- Blockchain version: 3.0.0.0
- Ledger version: 1000
```

***

### **Subscribe to Address Transactions**

**You:**

```
Subscribe to real-time transaction notifications for address ZTX3Ta7d4GyAXD41H2kFCTd2eXhDesM83rvC3
```

**Claude will:**

1. Use `zetrix_ws_connect` (if not connected)
2. Use `zetrix_ws_subscribe_tx`

**Note:** You'll be notified when transactions occur for this address in real-time.

***

### **Monitor Multiple Addresses**

**You:**

```
I want to monitor transactions for these addresses:
- ZTX3Ta7d4GyAXD41H2kFCTd2eXhDesM83rvC3
- ZTX4Ub8e5fHzBYE52J3lGDe3fyIiNf94sxwD4

Let me know immediately when any transactions happen.
```

**Claude will:**

* Subscribe to both addresses via WebSocket
* Notify you of any incoming/outgoing transactions

***
