# Claude Code Configuration

Claude Code is a VS Code extension that integrates Claude directly into your development environment.

**Installation Steps**

**1. Install the Extension**

* Open VS Code
* Go to Extensions (`Ctrl+Shift+X` / `Cmd+Shift+X`)
* Search for "Claude Code"
* Click Install

**2. Configure MCP Server**

* Open VS Code Settings (`Ctrl+,` / `Cmd+,`)
* Search for "Claude MCP"
* Add Zetrix MCP Server configuration

**3. Configuration Format**

```json
{
  "claude.mcpServers": {
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

**4. Reload VS Code**

* Press `Ctrl+Shift+P` / `Cmd+Shift+P`
* Type "Reload Window"
* Press Enter

**Advantages of Claude Code**

* **Integrated Development:** Write code and query blockchain in same window
* **Context Awareness:** Claude understands your project structure
* **Real-time Assistance:** Get blockchain data while coding
* **Debugging Support:** Test contracts and transactions inline
* **Version Control:** Easy Git integration for blockchain projects
