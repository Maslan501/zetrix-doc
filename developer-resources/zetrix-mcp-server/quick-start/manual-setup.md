# Manual Setup

For users who prefer manual configuration or troubleshooting:

**Step 1: Install & Build**

```bash
cd /path/to/zetrix-mcp-server
npm install
npm run build
```

**Step 2: Locate Claude Desktop Config File**

Find your configuration file:

**Windows:**

```
%APPDATA%\Claude\claude_desktop_config.json
```

(Typically: `C:\Users\YourName\AppData\Roaming\Claude\claude_desktop_config.json`)

**macOS:**

```
~/Library/Application Support/Claude/claude_desktop_config.json
```

**Linux:**

```
~/.config/Claude/claude_desktop_config.json
```

**Step 3: Choose Pre-Made Configuration**

The `configs/` folder contains ready-to-use configurations:

**For Mainnet:**

```
configs/mcp-config-windows-mainnet.json
configs/mcp-config-macos-mainnet.json
configs/mcp-config-linux-mainnet.json
```

**For Testnet:**

```
configs/mcp-config-windows-testnet.json
configs/mcp-config-macos-testnet.json
configs/mcp-config-linux-testnet.json
```

**For Both Networks:**

```
configs/mcp-config-windows-both.json
configs/mcp-config-macos-both.json
configs/mcp-config-linux-both.json
```

**Step 4: Update Path in Configuration**

**Critical:** Update the absolute path to match your installation.

**Example for Windows (Mainnet):**

```json
{
  "mcpServers": {
    "zetrix-mainnet": {
      "command": "node",
      "args": [
        "C:\\Users\\YourName\\zetrix-mcp-server\\dist\\index.js"
      ],
      "env": {
        "ZETRIX_NETWORK": "mainnet"
      }
    }
  }
}
```

**Example for macOS/Linux (Mainnet):**

```json
{
  "mcpServers": {
    "zetrix-mainnet": {
      "command": "node",
      "args": [
        "/Users/YourName/zetrix-mcp-server/dist/index.js"
      ],
      "env": {
        "ZETRIX_NETWORK": "mainnet"
      }
    }
  }
}
```

**Example for Both Networks:**

```json
{
  "mcpServers": {
    "zetrix-mainnet": {
      "command": "node",
      "args": [
        "/absolute/path/to/zetrix-mcp-server/dist/index.js"
      ],
      "env": {
        "ZETRIX_NETWORK": "mainnet"
      }
    },
    "zetrix-testnet": {
      "command": "node",
      "args": [
        "/absolute/path/to/zetrix-mcp-server/dist/index.js"
      ],
      "env": {
        "ZETRIX_NETWORK": "testnet"
      }
    }
  }
}
```

**Step 5: Restart Claude Desktop**

Completely quit and restart Claude Desktop.

**Step 6: Test**

Ask Claude:

```
"Check if the Zetrix node is healthy"
```
