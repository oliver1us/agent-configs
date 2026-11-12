# Make MCP for OpenCode

Connect OpenCode to Make scenarios through Make's remote MCP server. This configuration uses an API token in an `Authorization: Bearer` header, not OAuth.

## Zones

On standard Make dashboard hosts, the zone is the subdomain before Make's domain: `eu1`, `eu2`, `us1`, or `us2`. `eu`/`us` mean EU/US hosting regions; `1`/`2` distinguish hosting zones, not your country or local time zone. For example, in the **fictional** dashboard address `https://eu1.make.example.com/dashboard`, `eu1` is the zone. Use that same hostname with `/mcp/v2`, giving `https://eu1.make.example.com/mcp/v2` in this fictional example. Enterprise hostnames may differ; use your actual dashboard hostname.

## Setup

1. Install OpenCode and sign in to Make. Find your dashboard hostname in its address bar (see **Zones** above).
2. In Make, open **profile icon > Profile > API access > Tokens > + API token** (or **Add token**). Give it a label and the `mcp:use` scope, then add it and copy the token immediately: its full value is shown only once.
3. Set both required variables in the shell that will launch OpenCode (these values are fictional):
   ```sh
   export MAKE_MCP_URL='https://eu1.make.example.com/mcp/v2'
   export MAKE_MCP_TOKEN='your-token-here'
   ```
   Replace the example hostname with your **actual** dashboard hostname. Alternatively, keep both values in this folder's ignored `.env`, then load it **from this folder in the same shell** before starting OpenCode:
   ```sh
   set -a; . ./.env; set +a
   opencode
   ```
   OpenCode does not load `.env` automatically. Reloading configuration will not supply missing environment variables; after changing them, launch a new OpenCode process if needed.
4. Run `opencode` from this folder, or copy the `make` server entry under `mcp.servers` into your project's `opencode.json`. From that configured folder, run `opencode mcp list` and look for **connected**. A live connection has not been verified here.

## Safety

Expose only scenarios you intend to use; Make MCP Toolboxes can restrict them, but toolbox-specific endpoints are not interchangeable with this configuration's generic endpoint. Make also documents token-in-URL authentication, but URLs can leak into logs. Keep tokens out of URLs and `opencode.json`.
