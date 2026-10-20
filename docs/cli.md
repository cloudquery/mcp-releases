# Set up for the CloudQuery CLI

Get started with the CloudQuery CLI. Describe what you want to sync in plain language, and the server generates the configuration files.

[← Back to README](../README.md)

## Before you start

You need the MCP server installed. See [Install](../README.md#install).

CLI mode is the default. It needs no credentials and runs when no connection variables are set.

## Step 1: Add the server to your client

<details>
<summary><b>Claude Desktop</b></summary>

**Easiest:** download the `.mcpb` file for your platform from the [latest release](https://github.com/cloudquery/mcp-releases/releases/latest) and double-click it. Claude Desktop installs it as an extension.

**Or edit the config file:** open **Settings → Developer → Edit Config** and add:

```json
{
  "mcpServers": {
    "cloudquery": {
      "command": "/absolute/path/to/cq-platform-mcp"
    }
  }
}
```

Then quit Claude Desktop completely and reopen it.

</details>

<details>
<summary><b>Claude Code</b></summary>

```bash
claude mcp add cloudquery -- /absolute/path/to/cq-platform-mcp
```

</details>

<details>
<summary><b>Cursor</b></summary>

Add to `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "cloudquery": {
      "command": "/absolute/path/to/cq-platform-mcp"
    }
  }
}
```

</details>

<details>
<summary><b>VS Code</b></summary>

Run **MCP: Open User Configuration** from the Command Palette and add:

```json
{
  "servers": {
    "cloudquery": {
      "type": "stdio",
      "command": "/absolute/path/to/cq-platform-mcp"
    }
  }
}
```

</details>

## Step 2: Check that it works

Try:

- "Create a CloudQuery config that syncs AWS EC2 and S3 to PostgreSQL."
- "Which tables does the GCP source plugin support?"

## Next steps

After your first sync, connect the server to the data:

- Synced to PostgreSQL or Snowflake: [Set up with PostgreSQL or Snowflake](databases.md)
- Using CloudQuery Platform: [Set up with CloudQuery Platform](platform.md)
