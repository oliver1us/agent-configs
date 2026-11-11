# n8n MCP for OpenCode

Connect OpenCode to workflows exposed by n8n's instance-level MCP.

1. Enable **Instance-level MCP** in n8n and enable the workflows you want to expose. In **Settings > Instance-level MCP > Connect a client**, copy the **full** connection URL. It usually ends in `/mcp-server/http`; do not guess or append `/mcp`.
2. In the shell that will launch OpenCode, set `N8N_MCP_URL` to that HTTPS URL, for example:
   ```sh
   export N8N_MCP_URL='https://n8n.example.com/mcp-server/http'
   ```
   If you keep it in a locally ignored `.env`, load that file explicitly from its directory: `set -a; . ./.env; set +a`. OpenCode does **not** load `.env` automatically; changing the environment may require launching a new OpenCode process.
3. Run `opencode` from this directory, or copy the `n8n` entry under `mcp.servers` into your project's root `opencode.json`. In OpenCode, use `/mcps`, select **n8n**, and authorize with OAuth. Check with `opencode mcp list` from the configured project; look for **connected**. The connection has not yet been verified.

If OAuth is unavailable, use the token from n8n's **API key** tab: replace only the `n8n` server entry with this V2 alternative, and export `N8N_MCP_TOKEN` in the launching shell (or load it from an ignored `.env`):

```json
"n8n": {
  "type": "remote",
  "url": "{env:N8N_MCP_URL}",
  "oauth": false,
  "headers": {
    "Authorization": "Bearer {env:N8N_MCP_TOKEN}"
  }
}
```

Never put a token literal in JSON. Use HTTPS and expose only workflows the client should be allowed to access.
