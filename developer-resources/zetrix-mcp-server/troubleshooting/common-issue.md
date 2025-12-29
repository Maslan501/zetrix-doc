# Common Issue

### Tools Don't Appear in Claude

**Symptoms:**

* MCP tools list doesn't show Zetrix tools
* Claude doesn't recognize Zetrix commands

**Solutions:**

1. **Verify Configuration File Path**
   * Windows: `%APPDATA%\Claude\claude_desktop_config.json`
   * macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
   * Linux: `~/.config/Claude/claude_desktop_config.json`
2.  **Check Configuration Syntax**

    ```bash
    # Validate JSON
    node -e "console.log(JSON.parse(require('fs').readFileSync('path/to/claude_desktop_config.json')))"
    ```
3. **Verify Absolute Path**
   * Must use complete absolute path to `dist/index.js`
   * Windows: Use forward slashes `/` or escaped backslashes `\\`
   * Example: `C:/Users/YourName/zetrix-mcp-server/dist/index.js`
4. **Restart Claude Desktop Completely**
   * Windows: Right-click system tray → Quit → Relaunch
   * macOS: Cmd+Q → Relaunch
   * Linux: Close all windows → Relaunch
5. **Check MCP Server Logs**
   * Look for error messages in Claude Desktop developer console
   * Windows/Linux: `Ctrl+Shift+I`
   * macOS: `Cmd+Option+I`

***

### Module Not Found Error

**Symptoms:**

```
Error: Cannot find module 'zetrix-sdk-nodejs'
Error: Cannot find module 'zetrix-encryption-nodejs'
```

**Solutions:**

1.  **Install Dependencies**

    ```bash
    cd /path/to/zetrix-mcp-server
    npm install
    ```
2.  **Rebuild Project**

    ```bash
    npm run build
    ```
3.  **Verify Node Modules**

    ```bash
    ls node_modules/zetrix-sdk-nodejs
    ls node_modules/zetrix-encryption-nodejs
    ```
4.  **Clear Cache and Reinstall**

    ```bash
    rm -rf node_modules package-lock.json
    npm install
    npm run build
    ```

***

### WebSocket Connection Fails

**Symptoms:**

* "Failed to connect to WebSocket"
* "WebSocket connection timeout"
* Real-time monitoring not working

**Solutions:**

1. **Check Network Configuration**
   * Ensure WebSocket URLs are correct:
     * Mainnet: `ws://node-ws.zetrix.com`
     * Testnet: `ws://test-node-ws.zetrix.com`
2. **Verify Firewall Settings**
   * Allow outbound WebSocket connections
   * Check corporate firewall/proxy settings
   * Try using WSS (secure WebSocket) if available
3.  **Test Connection Manually**

    ```bash
    npm run test:websocket
    ```
4. **Use HTTP RPC as Fallback**
   * If WebSocket unavailable, use HTTP RPC tools
   * Poll with `zetrix_get_latest_block` instead of real-time

***

### Node.js Version Error

**Symptoms:**

```
Error: The engine "node" is incompatible with this module
Required: >=18.0.0
```

**Solutions:**

1.  **Check Current Version**

    ```bash
    node --version
    ```
2. **Upgrade Node.js**
   * Download from https://nodejs.org/
   * Install v18 LTS or higher
   * Verify: `node --version`
3.  **Use Version Manager (Optional)**

    ```bash
    # Using nvm (Node Version Manager)
    nvm install 18
    nvm use 18
    ```

***

### Transaction Submission Fails

**Symptoms:**

* "Invalid nonce"
* "Insufficient fee"
* "Transaction validation failed"

**Solutions:**

1.  **Check Account Nonce**

    ```
    Ask Claude: "What's the current nonce for address ZTX...?"
    ```

    * Use returned nonce + 1 for next transaction
2.  **Verify Sufficient Balance**

    ```
    Ask Claude: "What's the balance of ZTX...?"
    ```

    * Ensure balance > amount + fees
3.  **Test Transaction First**

    ```
    Ask Claude: "Test this transaction to estimate fees"
    ```

    * Use `zetrix_test_transaction` before submitting
4.  **Check Account Activation**

    ```
    Ask Claude: "Is account ZTX... activated?"
    ```

    * Unactivated accounts cannot send transactions

***

### Smart Contract Errors

**Symptoms:**

* Contract deployment fails
* Contract call returns errors
* Unexpected contract behavior

**Solutions:**

1. **Validate Contract Syntax**
   * Check ES5 compatibility (no ES6+ features)
   * Verify all functions are defined
   * Test with `TEST_INVOKE` and `TEST_QUERY`
2. **Check Gas Limits**
   * Increase `feeLimit` for complex contracts
   * Optimize contract code to reduce gas usage
3.  **Debug with Logging**

    ```javascript
    Utils.log('Debug: value = ' + value);
    Chain.tlog('Event', param1, param2);
    ```
4.  **Test in Sandbox First**

    ```
    Ask Claude: "Test this contract code in sandbox mode"
    ```

***

### Cryptography Errors

**Symptoms:**

* "Invalid private key format"
* "Signature verification failed"
* "Decryption error"

**Solutions:**

1.  **Validate Key Format**

    ```
    Ask Claude: "Validate this private key format: privbs..."
    ```
2. **Check Key Encoding**
   * Private keys start with `priv`
   * Public keys start with `b001`
   * Addresses start with `ZTX`
3. **Verify Password for Encrypted Keys**
   * Ensure correct password for decryption
   * Check for typos or encoding issues
4.  **Regenerate Keys if Corrupted**

    ```
    Ask Claude: "Generate a new secure keypair"
    ```

***

### Performance Issues

**Symptoms:**

* Slow query responses
* Timeout errors
* High latency

**Solutions:**

1. **Use Multi-Query for Bulk Operations**
   * Instead of sequential queries, use `zetrix_multi_query`
   * 3-5x faster for multiple requests
2. **Optimize WebSocket Usage**
   * Use WebSocket for real-time monitoring
   * Avoid polling HTTP endpoints repeatedly
3. **Use Lightweight Queries**
   * `get_account_base` instead of `get_account` when full data not needed
   * Query specific fields instead of entire objects
4. **Check Network Connectivity**
   * Test ping to node endpoints
   * Check for network congestion

***
