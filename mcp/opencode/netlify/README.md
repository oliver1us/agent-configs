# Netlify MCP

Runs the Netlify MCP server locally so OpenCode can work with Netlify sites and deploys.

## Setup

1. Install OpenCode and Node.js 22+ (including `npx`). Obtain a Netlify personal access token suitable for noninteractive use, with an appropriate expiry.
2. Export `NETLIFY_PERSONAL_ACCESS_TOKEN` from your secret manager into the shell that will launch OpenCode. If you already have a trusted local `.env` in this folder, you can load it instead:
   ```sh
   set -a; . ./.env; set +a
   ```
3. Launch `opencode` from this folder. For deploys, copy the `netlify` entry from this folder's `opencode.json` into your project's `opencode.json` under `mcp.servers`, then launch OpenCode from the project in the same exported environment.
4. Run `opencode mcp list` from the configured directory and look for `netlify` **connected**.

OpenCode does not load `.env` automatically. If you change a token or environment variable, launch a new OpenCode process with the updated environment; reloading configuration alone is not enough. This configuration runs `npx -y @netlify/mcp` without a pinned package version, so the installed version may change between launches.
