# API Reference

### Tool Schema

All MCP tools follow this standard schema:

```json
{
  "name": "tool_name",
  "description": "What this tool does",
  "inputSchema": {
    "type": "object",
    "properties": {
      "param1": {
        "type": "string",
        "description": "Parameter description"
      }
    },
    "required": ["param1"]
  }
}
```

***

### Response Format

#### **Success Response**

```json
{
  "content": [
    {
      "type": "text",
      "text": "Response data in JSON or formatted text"
    }
  ]
}
```

***

#### **Error Response**

```json
{
  "content": [
    {
      "type": "text",
      "text": "Error: [description]"
    }
  ],
  "isError": true
}
```

***

### Network Endpoints

#### **Mainnet**

| Service       | URL                       | Protocol |
| ------------- | ------------------------- | -------- |
| **HTTP RPC**  | `https://node.zetrix.com` | HTTPS    |
| **WebSocket** | `ws://node-ws.zetrix.com` | WS       |
| **Chain ID**  | 0                         | N/A      |
| **Purpose**   | Production                | N/A      |

***

#### **Testnet**

| Service       | URL                            | Protocol |
| ------------- | ------------------------------ | -------- |
| **HTTP RPC**  | `https://test-node.zetrix.com` | HTTPS    |
| **WebSocket** | `ws://test-node-ws.zetrix.com` | WS       |
| **Chain ID**  | 0                              | N/A      |
| **Purpose**   | Development & Testing          | N/A      |

***

#### **Custom RPC**

You can configure custom endpoints via environment variables:

```json
{
  "env": {
    "ZETRIX_NETWORK": "mainnet",
    "ZETRIX_RPC_URL": "https://your-custom-node.com",
    "ZETRIX_WS_URL": "ws://your-custom-websocket.com"
  }
}
```

***

### Rate Limits

#### **HTTP RPC:**

* Recommended: 10 requests/second
* Burst: 50 requests/second
* Use `zetrix_multi_query` to batch requests

#### **WebSocket:**

* Connections: 100 per IP
* Messages: 1000/second
* Subscriptions: 100 per connection

***
