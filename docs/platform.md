# Set up with CloudQuery Platform

Query the asset inventory in your CloudQuery Platform deployment using natural language.

[← Back to README](../README.md)

## Before you start

You need:

- The MCP server installed. See [Install](../README.md#install).
- Your Platform API URL: your deployment URL followed by `/api`, for example `https://acme.cloudquery.io/api`.
- A Platform API key that has not expired.
  <!-- TODO: add exact UI path to create an API key, required role/scope, and default expiry. -->

## Step 1: Add the server to your client

Pick your client. In every example, replace:

- `/absolute/path/to/cq-platform-mcp` with the full path to the binary (on Windows, `C:\\path\\to\\cq-platform-mcp.exe`)
- `your_api_key_here` with your API key
- `https://your-deployment.cloudquery.io/api` with your API URL

<details>
<summary><b>Claude Desktop</b></summary>

1. Open **Settings → Developer → Edit Config**. This opens `claude_desktop_config.json`:
   - macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`
   - Windows: `%APPDATA%\Claude\claude_desktop_config.json`
2. Add:

   ```json
   {
     "mcpServers": {
       "cloudquery": {
         "command": "/absolute/path/to/cq-platform-mcp",
         "env": {
           "CQ_PLATFORM_API_URL": "https://your-deployment.cloudquery.io/api",
           "CQ_PLATFORM_API_KEY": "your_api_key_here"
         }
       }
     }
   }
   ```

3. Quit Claude Desktop completely and reopen it.

</details>

<details>
<summary><b>Claude Code</b></summary>

```bash
claude mcp add cloudquery \
  --env CQ_PLATFORM_API_URL=https://your-deployment.cloudquery.io/api \
  --env CQ_PLATFORM_API_KEY=your_api_key_here \
  -- /absolute/path/to/cq-platform-mcp
```

</details>

<details>
<summary><b>Cursor</b></summary>

Add to `~/.cursor/mcp.json` (all projects) or `.cursor/mcp.json` (one project). Don't commit a project file that contains your key.

```json
{
  "mcpServers": {
    "cloudquery": {
      "command": "/absolute/path/to/cq-platform-mcp",
      "env": {
        "CQ_PLATFORM_API_URL": "https://your-deployment.cloudquery.io/api",
        "CQ_PLATFORM_API_KEY": "your_api_key_here"
      }
    }
  }
}
```

</details>

<details>
<summary><b>VS Code</b></summary>

Run **MCP: Open User Configuration** from the Command Palette and add the config below. VS Code prompts for the URL and key on first use and stores them securely, so they aren't saved in the file.

```json
{
  "inputs": [
    { "type": "promptString", "id": "cq-api-url", "description": "CloudQuery Platform API URL" },
    { "type": "promptString", "id": "cq-api-key", "description": "CloudQuery Platform API key", "password": true }
  ],
  "servers": {
    "cloudquery": {
      "type": "stdio",
      "command": "/absolute/path/to/cq-platform-mcp",
      "env": {
        "CQ_PLATFORM_API_URL": "${input:cq-api-url}",
        "CQ_PLATFORM_API_KEY": "${input:cq-api-key}"
      }
    }
  }
}
```

Restart VS Code if the server doesn't appear.

</details>

> Don't set `POSTGRES_CONNECTION_STRING` or `SNOWFLAKE_CONNECTION_STRING` for the same server entry.
<!-- TODO: confirm which mode wins when several connection variables are set. -->

## Step 2: Check that it works

1. Confirm `cloudquery` shows as connected in your client's MCP or tools list.
2. Try:
   - "How many AWS EC2 instances do we have, by region?"
   - "List S3 buckets that are publicly accessible."
   - "Which tables are available for GCP?"

## Troubleshooting

| Symptom | Fix |
|---|---|
| Server fails to start on macOS | Run `xattr -d com.apple.quarantine /path/to/cq-platform-mcp`. See [macOS](../README.md#macos-blocks-the-binary). |
| `401` / unauthorized | The key has expired or belongs to another deployment. Create a new key for the deployment in `CQ_PLATFORM_API_URL`. |
| `404` / connection errors | Check the URL ends with `/api` and has no trailing slash. |
| Server isn't listed | Check `command` is an absolute path and the JSON is valid, then fully restart the client. |

For more detail, turn on debug logs with `CQAPI_LOG_LEVEL=debug`. See [Logs](../README.md#logs) for where clients write them.
