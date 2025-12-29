# Network Configuration

**Environment Variables**

Configure network and endpoints via environment variables:

<table><thead><tr><th width="167">Variable</th><th>Description</th><th width="178.4544677734375">Default</th><th>Options</th></tr></thead><tbody><tr><td><code>ZETRIX_NETWORK</code></td><td>Network to connect to</td><td><code>mainnet</code></td><td><code>mainnet</code>, <code>testnet</code></td></tr><tr><td><code>ZETRIX_RPC_URL</code></td><td>Custom HTTP RPC endpoint</td><td>Auto</td><td>Any valid HTTPS URL</td></tr><tr><td><code>ZETRIX_WS_URL</code></td><td>Custom WebSocket endpoint</td><td>Auto</td><td>Any valid WS/WSS URL</td></tr></tbody></table>

**Example Configuration:**

```json
{
  "env": {
    "ZETRIX_NETWORK": "mainnet",
    "ZETRIX_RPC_URL": "https://custom-node.zetrix.com",
    "ZETRIX_WS_URL": "ws://custom-ws.zetrix.com"
  }
}
```

**Network Endpoints**

**Mainnet:**

* HTTP RPC: `https://node.zetrix.com`
* WebSocket: `ws://node-ws.zetrix.com`
* Chain ID: 0 (mainnet)
* Purpose: Production applications with real value

**Testnet:**

* HTTP RPC: `https://test-node.zetrix.com`
* WebSocket: `ws://test-node-ws.zetrix.com`
* Chain ID: 0 (testnet)
* Purpose: Development, testing, experimentation

**Switching Networks**

**Method 1: Update Configuration File**

Edit your Claude Desktop config:

```json
// Switch to Testnet
"env": {
  "ZETRIX_NETWORK": "testnet"
}

// Switch to Mainnet
"env": {
  "ZETRIX_NETWORK": "mainnet"
}
```

Then restart Claude Desktop.

**Method 2: Run Both Networks Simultaneously**

Configure two separate MCP servers:

```json
{
  "mcpServers": {
    "zetrix-mainnet": {
      "command": "npx",
      "args": ["-y", "zetrix-mcp-server"],
      "env": {"ZETRIX_NETWORK": "mainnet"}
    },
    "zetrix-testnet": {
      "command": "npx",
      "args": ["-y", "zetrix-mcp-server"],
      "env": {"ZETRIX_NETWORK": "testnet"}
    }
  }
}
```

Now you can specify which network:

* "Check mainnet balance of..."
* "Get testnet latest block..."

**Method 3: Custom RPC Endpoints**

Use custom or private nodes:

```json
{
  "env": {
    "ZETRIX_NETWORK": "mainnet",
    "ZETRIX_RPC_URL": "https://your-private-node.com",
    "ZETRIX_WS_URL": "ws://your-private-websocket.com"
  }
}
```

**Use Cases for Custom Endpoints:**

* Private node infrastructure
* Load-balanced RPC endpoints
* Regional nodes for lower latency
* Testing with local nodes
* Custom network configurations

**When to Use Testnet vs Mainnet**

**Use Testnet for:**

* ✅ Development and testing
* ✅ Learning blockchain operations
* ✅ Experimenting with transactions
* ✅ Smart contract deployment testing
* ✅ No real value at risk
* ✅ Free test tokens available

**Use Mainnet for:**

* ✅ Production applications
* ✅ Real value transactions
* ✅ Live smart contract deployment
* ✅ Enterprise operations
* ⚠️ Verified and tested code only
* ⚠️ Irreversible transactions

***
