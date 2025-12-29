# Verification

**Success Checklist**

Verify your installation with this checklist:

* ✅Node.js v18+ installed
* ✅Dependencies installed (`npm install` completed)
* ✅Server built (`npm run build` completed)
* ✅Config file updated with correct path
* ✅Claude Desktop completely restarted
* ✅Tools visible in Claude (🔨 icon in chat)
* ✅Health check responds successfully

**Testing Commands**

**Run Full Test Suite:**

```bash
npm test
```

**Test Specific Networks:**

```bash
npm run test:mainnet  # Test mainnet connectivity
npm run test:testnet  # Test testnet connectivity
```

**Manual Server Test:**

```bash
npm run dev
```

You should see:

```
Zetrix MCP Server running on stdio
```

Press `Ctrl+C` to exit.

**Quick Test Examples**

Try these commands in Claude Desktop:

**1. Node Health:**

```
"Is the Zetrix node healthy?"
```

**2. Latest Block:**

```
"What's the latest block on Zetrix?"
```

**3. Account Balance:**

```
"What's the balance of address ZTX3Ta7d4GyAXD41H2kFCTd2eXhDesM83rvC3?"
```

**4. Generate Keypair:**

```
"Generate a new Zetrix test keypair"
```

**5. WebSocket Connection:**

```
"Connect to Zetrix WebSocket and show me the blockchain version"
```

***
