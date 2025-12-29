# Installation Methods

**Method 1: NPM Global Installation (Recommended)**

**Best for:** Most users who want simple system-wide access.

**Installation:**

```bash
npm install -g zetrix-mcp-server
```

**What this does:**

* Downloads latest stable version from NPM
* Installs in global node\_modules directory
* Creates executable binaries accessible system-wide
* Includes all dependencies
* Size: \~15-20 MB
* Time: 30-60 seconds

**Verification:**

```bash
npx zetrix-mcp-server --version
```

**Configuration:**

For global installation, use `npx` in your Claude config:

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

**Advantages:**

* ✅ Simple one-command installation
* ✅ Always uses latest published version
* ✅ Easy updates: `npm update -g zetrix-mcp-server`
* ✅ Works across multiple projects
* ✅ No local repository management

**Disadvantages:**

* ❌ Requires administrative permissions on some systems
* ❌ Cannot modify source code
* ❌ Updates require manual reinstallation

**Method 2: From Source (Development)**

**Best for:** Developers contributing to the project or needing custom modifications.

**Installation:**

```bash
# Clone repository
git clone https://github.com/Zetrix-Chain/zetrix-mcp-server.git
cd zetrix-mcp-server

# Install dependencies
npm install

# Build project
npm run build

# Run setup wizard
npm run setup
```

**What this does:**

* Downloads complete source code (\~5 MB)
* Installs development dependencies (\~30 MB)
* Compiles TypeScript to JavaScript
* Creates dist/ directory
* Sets up local development environment
* Build time: 10-20 seconds

**Configuration:**

For source installation, use absolute path in Claude config:

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

**Advantages:**

* ✅ Full access to source code
* ✅ Can create custom features and fixes
* ✅ Run tests and development builds
* ✅ Contribute changes back to project
* ✅ Use latest unreleased features from GitHub

**Disadvantages:**

* ❌ More complex setup process
* ❌ Requires Git knowledge
* ❌ Manual updates via `git pull`
* ❌ Larger disk space usage
* ❌ Need to rebuild after changes

**Method 3: NPX (No Installation)**

**Best for:** Quick testing, CI/CD pipelines, or minimal disk usage.

**Usage:**

```bash
npx -y zetrix-mcp-server
```

**What this does:**

* Downloads package to temporary NPM cache
* Executes directly without permanent installation
* Automatically uses latest version
* Cache location: `~/.npm/_npx/`

**Configuration:**

Same as Method 1 (NPM Global):

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

**Advantages:**

* ✅ Zero installation required
* ✅ Always runs latest version
* ✅ Perfect for CI/CD automation
* ✅ No administrative permissions needed
* ✅ Minimal disk footprint

**Disadvantages:**

* ❌ Slightly slower startup (downloads on first run)
* ❌ Requires internet connection
* ❌ May download repeatedly
* ❌ Not suitable for offline usage
* ❌ Less control over versioning
