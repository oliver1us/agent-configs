# Render MCP

Connects OpenCode to Render's hosted MCP server to manage Render resources.

## Setup

1. Install OpenCode and obtain a Render API key limited to the workspace and permissions you need.
2. Export `RENDER_API_KEY` from your secret manager into the shell that will launch OpenCode. If you already have a trusted local `.env` in this folder, you can load it instead:
   ```sh
   set -a; . ./.env; set +a
   ```
3. Launch `opencode` from this folder, or copy the `render` entry from this folder's `opencode.json` into your project's `opencode.json` under `mcp.servers` and launch from that project in the same exported environment.
4. Run `opencode mcp list` from the configured directory and look for `render` **connected**. Select the intended workspace before using its tools.

This configuration uses API-key authorization and disables OAuth for this connection. OpenCode does not load `.env` automatically: after changing the environment, launch a new OpenCode process; reloading configuration alone is not enough. Connection has not been verified live.
