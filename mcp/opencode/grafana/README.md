# Grafana MCP

This local MCP connects OpenCode to Grafana through `uvx`. A live connection has not been tested.

## Setup

1. Install OpenCode, `uv` (including `uvx`), and Python 3.10+.
2. In Grafana, create a service account with Viewer/read permissions and generate a service account token. Use this instead of a deprecated Grafana API key.
3. In this folder, create a local `.env` containing the required variables (replace these fictional values with yours):

   ```sh
   GRAFANA_URL=https://grafana.example.com
   GRAFANA_SERVICE_ACCOUNT_TOKEN=your-token-here
   ```

4. From the repository root, load the trusted `.env` into the shell that launches OpenCode:

   ```sh
   cd mcp/opencode/grafana
   set -a
   . ./.env
   set +a
   opencode mcp list
   opencode
   ```

Check that `grafana` shows **connected** in the list. To test access, ask OpenCode: “Using Grafana, list the dashboards I can read. Do not change anything.”

Keep `.env` private. OpenCode does not load it automatically; reloading configuration alone cannot supply missing variables. After changing `.env`, source it again and start a new OpenCode process. If an existing shared service still has the old environment, restart it before reconnecting. To use this MCP in another project, copy only the `grafana` entry into that project's `mcp.servers` and provide the same variables in its launch environment.
