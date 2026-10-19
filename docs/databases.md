# Set up with PostgreSQL or Snowflake

Query data that the CloudQuery CLI synced to a PostgreSQL or Snowflake destination using natural language.

[← Back to README](../README.md)

## Before you start

You need:

- The MCP server binary installed. See [Install](../README.md#install).
- At least one completed CloudQuery sync to PostgreSQL or Snowflake.
- A connection string for that database (see Step 1).

**Use a read-only database user.** The server runs queries that the AI writes. A read-only user keeps a bad query from changing your data.
<!-- TODO: confirm whether the server enforces read-only queries itself. -->

## Step 1: Build your connection string

### PostgreSQL

```
postgres://user:password@host:5432/database?sslmode=require
```

- **Schema:** the server uses `public` by default. To use another schema, add `search_path`, for example `?search_path=cloudquery`.
- **SSL:** use `sslmode=require` (or stricter) for remote databases. Use `sslmode=disable` only for a local database.
- **Special characters:** URL-encode them in the password. For example, `p@ss` becomes `p%40ss`.
- **Kerberos/GSSAPI:** leave out the password and add `krbsrvname`:
  - `postgres://username@REALM.EXAMPLE.COM@host:5432/database?krbsrvname=postgres`
  - `postgres://username@host:5432/database?krbsrvname=postgres&gsslib=gssapi`

Read-only user example:

```sql
CREATE USER cq_mcp WITH PASSWORD '...';
GRANT CONNECT ON DATABASE cloudquery TO cq_mcp;
GRANT USAGE ON SCHEMA public TO cq_mcp;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO cq_mcp;
ALTER DEFAULT PRIVILEGES IN SCHEMA public GRANT SELECT ON TABLES TO cq_mcp;
```

### Snowflake

```
user:password@account/database/schema?warehouse=COMPUTE_WH
```

- `account` is your account identifier, for example `myorg-myaccount`. Older accounts may need the region, for example `xy12345.us-east-1`.
- Use the database and schema your sync writes to.
- Set `warehouse` unless the user has a default warehouse.
- URL-encode special characters in the password.
- Other options, including key-pair authentication, are in the [Snowflake Go driver docs](https://pkg.go.dev/github.com/snowflakedb/gosnowflake/v2#hdr-Connection_String).
  <!-- TODO: confirm key-pair / SSO auth works and add an example. -->

## Step 2: Add the server to your client

Pick your client. In every example, replace `/absolute/path/to/cq-platform-mcp` with the full path to the binary (on Windows, `C:\\path\\to\\cq-platform-mcp.exe`).

The examples use PostgreSQL. For Snowflake, change the variable name to `SNOWFLAKE_CONNECTION_STRING` and use your Snowflake connection string. Set only one of the two.

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
           "POSTGRES_CONNECTION_STRING": "postgres://user:password@host:5432/database?sslmode=require"
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
  --env POSTGRES_CONNECTION_STRING='postgres://user:password@host:5432/database?sslmode=require' \
  -- /absolute/path/to/cq-platform-mcp
```

</details>

<details>
<summary><b>Cursor</b></summary>

Add to `~/.cursor/mcp.json` (all projects) or `.cursor/mcp.json` (one project). Don't commit a project file that contains credentials.

```json
{
  "mcpServers": {
    "cloudquery": {
      "command": "/absolute/path/to/cq-platform-mcp",
      "env": {
        "POSTGRES_CONNECTION_STRING": "postgres://user:password@host:5432/database?sslmode=require"
      }
    }
  }
}
```

</details>

<details>
<summary><b>VS Code</b></summary>

Run **MCP: Open User Configuration** from the Command Palette and add the config below. VS Code prompts for the connection string on first use and stores it securely, so it isn't saved in the file.

```json
{
  "inputs": [
    { "type": "promptString", "id": "cq-db-conn", "description": "Database connection string", "password": true }
  ],
  "servers": {
    "cloudquery": {
      "type": "stdio",
      "command": "/absolute/path/to/cq-platform-mcp",
      "env": {
        "POSTGRES_CONNECTION_STRING": "${input:cq-db-conn}"
      }
    }
  }
}
```

Restart VS Code if the server doesn't appear.

</details>

## Step 3: Check that it works

1. Confirm `cloudquery` shows as connected in your client's MCP or tools list.
2. Try:
   - "Which CloudQuery tables are in the database?"
   - "How many AWS EC2 instances do we have, by region?"
   - "List IAM users without MFA."

## Troubleshooting

| Symptom | Fix |
|---|---|
| Server fails to start on macOS | Run `xattr -d com.apple.quarantine /path/to/cq-platform-mcp`. See [macOS](../README.md#macos-blocks-the-binary). |
| "No tables found" or wrong tables | Set `search_path` (PostgreSQL) or the schema in the path (Snowflake) to the schema your sync writes to. |
| Authentication failed | URL-encode special characters in the password. |
| SSL / TLS errors (PostgreSQL) | Match `sslmode` to the server: `require` for most cloud databases, `disable` only for local ones. |
| Snowflake warehouse errors | Add `?warehouse=...`, or give the user a default warehouse. |
| Server isn't listed | Check `command` is an absolute path and the JSON is valid, then fully restart the client. |

For more detail, turn on debug logs with `CQAPI_LOG_LEVEL=debug`. See [Logs](../README.md#logs) for where clients write them.
