# SonarQube MCP

This local MCP lets OpenCode use SonarQube. The connection has not been tested yet.

## Setup

1. Install OpenCode and Node.js/npm (including `npx`), and get access to SonarQube.
2. In SonarQube, open your profile menu → **My Account** → **Security**. Generate a **User** token from an account with only the permissions this MCP needs, and copy it immediately (SonarQube shows it only once). Revoke the previously exposed token on the same page, then replace `SONARQUBE_TOKEN` in the local `.env` with the new one.
3. From this folder, load the trusted, shell-compatible `.env` in the shell that will launch OpenCode:

   ```sh
   set -a
   . ./.env
   set +a
   opencode mcp list
   opencode
   ```

The current `opencode.json` is already configured. It requires these variables:

- `SONARQUBE_URL` — your SonarQube server URL.
- `SONARQUBE_TOKEN` — your SonarQube API token.

Check that `sonarqube` shows **connected** in `opencode mcp list`. OpenCode does not load `.env` automatically: if it changes, load it again and launch a new OpenCode process. A running shared service may also need a restart to pick up the new environment.
