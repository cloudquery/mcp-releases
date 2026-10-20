# CloudQuery MCP Server

A Model Context Protocol (MCP) server that lets AI assistants query your CloudQuery asset inventory data. Works with Claude Desktop, Claude Code, Cursor, VS Code and other MCP clients.

## Choose your setup

| Where your data is                                      | Guide                                                    |
| ------------------------------------------------------- | -------------------------------------------------------- |
| PostgreSQL or Snowflake, synced with the CloudQuery CLI | [Set up with PostgreSQL or Snowflake](docs/databases.md) |
| CloudQuery Platform (Saas)                              | [Set up with CloudQuery Platform](docs/platform.md)      |
| No data yet; want help writing CLI configs              | [Set up for the CloudQuery CLI](docs/cli.md)             |

Each guide covers credentials, client configuration, a quick check and troubleshooting. [Install](#install) the server first.

## Install

### From the MCP Registry (recommended)

The server is listed in the official MCP Registry as `io.github.cloudquery/mcp`. If your client installs from the registry, use that name, then set the variables from your guide.

### Download the binary

1. Download the `.zip` for your platform from the [latest release](https://github.com/cloudquery/mcp-releases/releases/latest):
   - macOS Apple Silicon: `darwin_arm64`
   - macOS Intel: `darwin_amd64`
   - Linux: `linux_amd64` or `linux_arm64`
   - Windows: `windows_amd64`
2. Unzip it and move the binary to a permanent location, for example:

   ```bash
   unzip cq-platform-mcp_*_darwin_arm64.zip -d cq-mcp
   mkdir -p ~/bin && mv cq-mcp/cq-platform-mcp ~/bin/
   ```

3. Note the absolute path, for example `/Users/you/bin/cq-platform-mcp`. Your client config needs it. `~` and relative paths don't work.

### macOS blocks the binary

The binary isn't signed with an Apple Developer certificate, so macOS may block it the first time. Remove the quarantine flag:

```bash
xattr -d com.apple.quarantine ~/bin/cq-platform-mcp
```

Or open **System Settings → Privacy & Security** and click **Allow Anyway**.

### Upgrade

Download the new release and replace the binary at the same path. Then restart your client.

## Reference

### Modes

The server picks a mode from the variables you set:

| Mode        | Set                                          | Guide                             |
| ----------- | -------------------------------------------- | --------------------------------- |
| `platform`  | `CQ_PLATFORM_API_URL`, `CQ_PLATFORM_API_KEY` | [platform.md](docs/platform.md)   |
| `postgres`  | `POSTGRES_CONNECTION_STRING`                 | [databases.md](docs/databases.md) |
| `snowflake` | `SNOWFLAKE_CONNECTION_STRING`                | [databases.md](docs/databases.md) |
| `cli`       | None (default)                               | [cli.md](docs/cli.md)             |

Set variables for one mode only.

### Optional variables

| Variable          | Description                                          | Default                   |
| ----------------- | ---------------------------------------------------- | ------------------------- |
| `CQAPI_LOG_LEVEL` | `debug`, `info`, `warn` or `error`                   | `info`                    |
| `LOG_PRETTY`      | Pretty console logs                                  | On when run in a terminal |
| `LOG_COLOR`       | Coloured logs                                        | On when run in a terminal |
| `HTTP_ADDRESS`    | Run as a Streamable HTTP server, for example `:8080` | Off (stdio)               |

The server also reads variables from a `.env` file in its working directory. MCP clients often start the server in a different directory, so set variables in the client config instead.

### Streamable HTTP server

To connect to the server remotely, set `HTTP_ADDRESS`:

```bash
HTTP_ADDRESS=":8080" CQ_PLATFORM_API_URL=... CQ_PLATFORM_API_KEY=... ./cq-platform-mcp
```

The server listens at `/mcp`, for example `http://localhost:8080/mcp`.

> **Security:** anyone who can reach this endpoint can query your data with the server's credentials. Bind it to `127.0.0.1` or put it behind an authenticating proxy. Don't expose it to the internet.

### Logs

| Client                   | Where to find MCP server logs                           |
| ------------------------ | ------------------------------------------------------- |
| Claude Desktop (macOS)   | `~/Library/Logs/Claude/mcp-server-cloudquery.log`       |
| Claude Desktop (Windows) | `%APPDATA%\Claude\logs\mcp-server-cloudquery.log`       |
| Claude Code              | Run `claude --debug`, or `/mcp` to see status           |
| Cursor                   | **Output** panel → **MCP Logs**                         |
| VS Code                  | **MCP: List Servers** → select server → **Show Output** |

## License

See [LICENSE](LICENSE).
