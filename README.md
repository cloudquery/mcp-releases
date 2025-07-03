# CloudQuery MCP Server

A Model Context Protocol (MCP) server that provides access to CloudQuery asset inventory data.

## Prerequisites

- Claude Desktop, Cursor IDE or any other tool capable of using MCP servers.
- If you're using it against CloudQuery Platform, a non-expired CloudQuery Platform API Key and the URL of the deployment it is valid for.
- If you're using it against a PostgreSQL destination (from one or more CloudQuery syncs), the PostgreSQL connection string.

## Installation

### Download Binary
Download the latest binary for your platform from the [Releases page](https://github.com/cloudquery/mcp-releases/releases).

### macOS Security Note
On macOS, you may encounter a security warning when running the binary for the first time. This is because the binary is not signed with an Apple Developer certificate. To bypass this:

**Option 1 (Recommended)**: Use the command line to remove the quarantine attribute:
```bash
xattr -d com.apple.quarantine /path/to/cq-platform-mcp
```

**Option 2**: Go to System Preferences > Security & Privacy > General, and click "Allow Anyway" for the blocked app.

### Required Environment Variables (can use .env)

Either (Platform Mode):

- `CQ_PLATFORM_API_URL` - "https://your-deployment.cloudquery.io/api"
- `CQ_PLATFORM_API_KEY` - Your CloudQuery Platform API key

Or (PostgreSQL Mode):

- `POSTGRES_CONNECTION_STRING` - "postgres://user:password@host:port/database"

### Optional Environment Variables

- `CQAPI_LOG_LEVEL` - Log level: debug, info, warn, error (default: `info`)
- `LOG_PRETTY` - Enable pretty console logging (default: auto-detected based on TTY)
- `LOG_COLOR` - Enable colored logging (default: auto-detected based on TTY)

## Claude Desktop Integration

Configure Claude Desktop by editing the config file at `$HOME/Library/Application\ Support/Claude/claude_desktop_config.json` on MacOS or `$HOME/.config/Claude/claude_desktop_config.json` on Linux:

If you're using it against CloudQuery Platform:

```json
{
  "mcpServers": {
    "cloudquery": {
      "command": "/absolute/path/to/cq-platform-mcp",
      "args": [],
      "env": {
        "CQ_PLATFORM_API_KEY": "your_api_key_here",
        "CQ_PLATFORM_API_URL": "https://your-instance.cloudquery.io/api"
      }
    }
  }
}
```

If you're using it against a PostgreSQL destination, e.g.:

```json
{
  "mcpServers": {
    "cloudquery": {
      "command": "/absolute/path/to/cq-platform-mcp",
      "args": [],
      "env": {
        "POSTGRES_CONNECTION_STRING": "postgres://user:password@localhost:5432/database?sslmode=disable"
      }
    }
  }
}
```

Notes: 
* "command" must be an absolute path to your local cq-platform-mcp binary.

Example: "/home/username/cq-platform-mcp" or "/Users/username/cq-platform-mcp"


## Cursor IDE Integration

Configure Cursor IDE by adding the MCP server configuration to your settings:

1. Open Cursor IDE
2. Go to **Settings** > **Cursor Settings** > **Tools and Integrations**
3. Add a new MCP server with the following configuration:

```json
{
  "name": "cloudquery",
  "command": "/path/to/mcp/binary",
  "args": [],
  "env": {
    "CQ_PLATFORM_API_KEY": "your_api_key_here",
    "CQ_PLATFORM_API_URL": "https://your-instance.cloudquery.io/api"
  }
}
```
