# Automatic Setup (Recommended)

The fastest way to get started using the interactive setup wizard:

**Step 1: Install Dependencies**

```bash
cd /path/to/zetrix-mcp-server
npm install
```

**What this does:**

* Downloads all required packages (\~30 MB)
* Installs zetrix-sdk-nodejs, zetrix-encryption-nodejs
* Sets up development dependencies
* Takes approximately 30-60 seconds

**Step 2: Build the Server**

```bash
npm run build
```

**What this does:**

* Compiles TypeScript to JavaScript
* Creates production-ready dist/ folder
* Generates source maps for debugging
* Takes approximately 10-20 seconds

**Step 3: Run Setup Wizard**

```bash
npm run setup
```

**The wizard will:**

1. ✅ **Verify Installation**
   * Check Node.js version (requires v18+)
   * Confirm npm installation
   * Validate project build
2. ✅ **Locate Claude Desktop Config**
   * Auto-detect your operating system
   * Find Claude Desktop config file
   * Create backup of existing config
3. ✅ **Network Selection**
   * Mainnet only
   * Testnet only
   * Both networks (recommended for development)
4. ✅ **Update Configuration**
   * Generate appropriate JSON config
   * Merge with existing MCP servers
   * Validate syntax
5. ✅ **Optional Testing**
   * Run HTTP RPC tests
   * Validate SDK operations
   * Check cryptography functions

**Step 4: Restart Claude Desktop**

**Important:** Complete shutdown and restart

* **Windows:** Right-click system tray → Quit, then relaunch
* **macOS:** Cmd+Q or Claude menu → Quit, then relaunch
* **Linux:** Close all windows, then relaunch

**Step 5: Verify**

In Claude Desktop, ask:

```
"What MCP tools do you have available?"
```

**Expected Result:** You should see 44 Zetrix tools listed! ✨

Try a quick test:

```
"Check if the Zetrix node is healthy"
```
