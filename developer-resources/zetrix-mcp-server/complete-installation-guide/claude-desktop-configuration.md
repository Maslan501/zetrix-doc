# Claude Desktop Configuration

## **Interactive Setup Wizard (Recommended)**

**For local installations from source:**

```bash
npm run setup
```

**Wizard Steps:**

**1. Verify Installation**

* Checks Node.js version (requires v18+)
* Verifies npm installation
* Confirms project build succeeded
* Validates all dependencies installed

**2. Locate Claude Desktop Config**

* Auto-detects operating system
* Searches standard config locations
* Verifies config file exists and is readable
* Creates backup (.backup file)
* Validates JSON syntax

**3. Network Selection**

* Mainnet only
* Testnet only
* Both networks (runs two separate servers)

**4. Update Configuration**

* Generates appropriate JSON configuration
* Merges with existing MCP servers (preserves others)
* Validates final configuration syntax
* Writes updated config to Claude Desktop

**5. Optional Testing**

* Offers to run comprehensive test suite
* Tests HTTP RPC connectivity
* Validates SDK operations
* Checks cryptography functions
* Provides detailed test results

## **Manual Configuration**

**Step 1: Locate Config File**

<table><thead><tr><th width="180.3636474609375">Operating System</th><th>Config File Location</th></tr></thead><tbody><tr><td><strong>Windows</strong></td><td><code>%APPDATA%\Claude\claude_desktop_config.json</code></td></tr><tr><td><strong>macOS</strong></td><td><code>~/Library/Application Support/Claude/claude_desktop_config.json</code></td></tr><tr><td><strong>Linux</strong></td><td><code>~/.config/Claude/claude_desktop_config.json</code></td></tr></tbody></table>

**Step 2: Edit Configuration**

Open the file in a text editor and add the Zetrix MCP Server configuration.

**Mainnet Configuration:**

```json
{
  "mcpServers": {
    "zetrix": {
      "command": "npx",
      "args": ["-y", "zetrix-mcp-server"],
      "env": {
        "ZETRIX_NETWORK": "mainnet"
      }
    }
  }
}
```

**Testnet Configuration:**

```json
{
  "mcpServers": {
    "zetrix": {
      "command": "npx",
      "args": ["-y", "zetrix-mcp-server"],
      "env": {
        "ZETRIX_NETWORK": "testnet"
      }
    }
  }
}
```

**Both Networks (Dual Setup):**

```json
{
  "mcpServers": {
    "zetrix-mainnet": {
      "command": "npx",
      "args": ["-y", "zetrix-mcp-server"],
      "env": {
        "ZETRIX_NETWORK": "mainnet"
      }
    },
    "zetrix-testnet": {
      "command": "npx",
      "args": ["-y", "zetrix-mcp-server"],
      "env": {
        "ZETRIX_NETWORK": "testnet"
      }
    }
  }
}
```

**Local Development (From Source):**

```json
{
  "mcpServers": {
    "zetrix": {
      "command": "node",
      "args": [
        "/absolute/path/to/zetrix-mcp-server/dist/index.js"
      ],
      "env": {
        "ZETRIX_NETWORK": "mainnet"
      }
    }
  }
}
```

**Step 3: Restart Claude Desktop**

Complete shutdown and restart for changes to take effect.
