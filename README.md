# CloudQuery MCP Server

A Model Context Protocol (MCP) server that provides access to CloudQuery asset inventory data.

## Prerequisites

- Claude Desktop, Cursor IDE or any other tool capable of using MCP servers.
- If you're using it against CloudQuery Platform, a non-expired CloudQuery Platform API Key and the URL of the deployment it is valid for.
- If you're using it against a PostgreSQL destination (from one or more CloudQuery syncs), the PostgreSQL connection string.

## Installation

### From MCP Registry (Recommended)

The CloudQuery MCP server is available through the official MCP Registry. This is the recommended way to install and use the server.

**For Claude Desktop users:**
1. Open Claude Desktop
2. Go to Settings → MCP Servers
3. Add a new server with the identifier: `io.github.cloudquery/mcp`
4. Configure the appropriate environment variables for your use case (see configuration sections below)

**For other MCP clients:**
Use the server identifier `io.github.cloudquery/mcp` to install from the MCP Registry.

### Download Binary

Download the latest binary for your platform from the [Releases page](https://github.com/cloudquery/mcp/releases).

### macOS Security Note

On macOS, you may encounter a security warning when running the binary for the first time. This is because the binary is not signed with an Apple Developer certificate. To bypass this:

**Option 1 (Recommended)**: Use the command line to remove the quarantine attribute:

```bash
xattr -d com.apple.quarantine /path/to/cq-platform-mcp
```

**Option 2**: Go to System Preferences > Security & Privacy > General, and click "Allow Anyway" for the blocked app.

### Supported modes

The MCP server supports four modes:

- `cli` - for getting started with CloudQuery CLI and generate configuration files using natural language
- `postgres` - for CLI users syncing to a PostgreSQL destination, to query the data synced to the database using natural language
- `snowflake` - for CLI users syncing to a Snowflake destination, to query the data synced to the database using natural language
- `platform` - for CloudQuery Platform customers to query the data synced to the platform using natural language

#### CLI Mode

If no environment variables are set, the MCP server will default to CLI mode.

#### PostgreSQL Mode

Configure the following environment variables to enable PostgreSQL mode.

- `POSTGRES_CONNECTION_STRING` - `postgres://user:password@host:port/database`

> **Note:** If no `search_path` is specified in the connection string, the MCP server automatically defaults to `public` schema. You can override this by explicitly setting `search_path` in your connection string (e.g., `postgres://user:password@host:port/database?search_path=myschema` or `host=localhost dbname=mydb search_path=myschema`).

##### Kerberos Authentication
For Kerberos/GSSAPI authentication, include the appropriate parameters in the connection string:
- `postgres://username@REALM.EXAMPLE.COM@host:port/database?krbsrvname=postgres`
- `postgres://username@host:port/database?krbsrvname=postgres&gsslib=gssapi`

> The MCP server supports reading `.env` files.

#### Snowflake Mode

Configure the following environment variables to enable Snowflake mode.

- `SNOWFLAKE_CONNECTION_STRING` - Snowflake connection string in the format: `user:password@account/database/schema?warehouse=warehouse_name`

Example connection strings:
- `user:password@account/database/schema` - Basic authentication
- `user:password@account/database/schema?warehouse=COMPUTE_WH` - With warehouse specified
- `user:password@account.region/database/schema?warehouse=COMPUTE_WH` - With region

For more connection string options, see the [Snowflake Go Driver documentation](https://pkg.go.dev/github.com/snowflakedb/gosnowflake).

> The MCP server supports reading `.env` files.

#### Platform Mode

Configure the following environment variables to enable Platform mode.

- `CQ_PLATFORM_API_URL` - `https://your-deployment.cloudquery.io/api`
- `CQ_PLATFORM_API_KEY` - Your CloudQuery Platform API key

> The MCP server supports reading `.env` files.

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

If you're using it against a Snowflake destination, e.g.:

```json
{
  "mcpServers": {
    "cloudquery": {
      "command": "/absolute/path/to/cq-platform-mcp",
      "args": [],
      "env": {
        "SNOWFLAKE_CONNECTION_STRING": "user:password@account/database/schema?warehouse=COMPUTE_WH"
      }
    }
  }
}
```

Notes: 

- "command" must be an absolute path to your local cq-platform-mcp binary.

Example: "/home/username/cq-platform-mcp" or "/Users/username/cq-platform-mcp"

## Cursor IDE Integration

Configure Cursor IDE by adding the MCP server configuration to your settings:

1. Open Cursor IDE
2. Go to **Settings** > **Cursor Settings** > **Tools and Integrations**
3. Add a new MCP server with the following configuration:

```json
{
  "mcpServers": {
    "cloudquery": {
      "command": "/path/to/mcp/binary",
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
      "command": "/path/to/mcp/binary",
      "args": [],
      "env": {
        "POSTGRES_CONNECTION_STRING": "postgres://user:password@localhost:5432/database?sslmode=disable"
      }
    }
  }
}
```

If you're using it against a Snowflake destination, e.g.:

```json
{
  "mcpServers": {
    "cloudquery": {
      "command": "/path/to/mcp/binary",
      "args": [],
      "env": {
        "SNOWFLAKE_CONNECTION_STRING": "user:password@account/database/schema?warehouse=COMPUTE_WH"
      }
    }
  }
}
```

## VSCode IDE Integration

There are multiple ways to integrate MCP servers in VSCode, as described in the [VSCode MCP documentation](https://code.visualstudio.com/docs/copilot/chat/mcp-servers#_use-mcp-tools-in-agent-mode).
A summary of the steps to integrate the CloudQuery MCP server in VSCode is:

1. Open VSCode IDE
2. Go to `MCP: Open User Configuration` (can also be done at the workspace or folder level) via the keyboard shortcut `Ctrl+Shift+P` (Windows/Linux) or `Cmd+Shift+P` (Mac)
3. Add a new MCP server with the following configuration:

If you're using it against CloudQuery Platform:

```json
{
  "inputs":[
    {
      "type":"promptString",
      "id":"cloudquery-platform-api-key",
      "description":"CloudQuery Platform API Key",
      "password":true
    },
    {
      "type":"promptString",
      "id":"cloudquery-platform-api-url",
      "description":"CloudQuery Platform API URL",
      "password":false
    }
  ],
  "servers":{
    "CloudQuery":{
      "type":"stdio",
      "command":"/path/to/mcp/binary",
      "env":{
        "CQ_PLATFORM_API_KEY": "${input:cloudquery-platform-api-key}",
        "CQ_PLATFORM_API_URL": "${input:cloudquery-platform-api-url}"
      }
    }
  }
}
```

If you're using it against a PostgreSQL destination, e.g.:

```json
{
  "inputs":[
    {
      "type":"promptString",
      "id":"cloudquery-postgres-connection-string",
      "description":"CloudQuery Postgres Connection String",
      "password":true
    }
  ],
  "servers":{
    "CloudQuery":{
      "type":"stdio",
      "command":"/path/to/mcp/binary",
      "env":{
        "POSTGRES_CONNECTION_STRING": "${input:cloudquery-postgres-connection-string}"
      }
    }
  }
}
```

If you're using it against a Snowflake destination, e.g.:

```json
{
  "inputs":[
    {
      "type":"promptString",
      "id":"cloudquery-snowflake-connection-string",
      "description":"CloudQuery Snowflake Connection String",
      "password":true
    }
  ],
  "servers":{
    "CloudQuery":{
      "type":"stdio",
      "command":"/path/to/mcp/binary",
      "env":{
        "SNOWFLAKE_CONNECTION_STRING": "${input:cloudquery-snowflake-connection-string}"
      }
    }
  }
}
```

To see the new MCP server in the list, you might need to restart VSCode.

> On first use, you will be prompted to enter the API key and API URL, or the PostgreSQL connection string.

## Streamable HTTP Server

The MCP server can also be run as a Streamable HTTP server, which allows you to connect to it remotely. To enable this,
set the `HTTP_ADDRESS` environment variable to the desired address and port, e.g.:

```bash
export HTTP_ADDRESS=":8080"
```

The server will then listen for incoming HTTP requests under the path `/mcp` on the specified address and port.