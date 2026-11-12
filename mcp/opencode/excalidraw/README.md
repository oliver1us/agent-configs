# Excalidraw MCP

Use Excalidraw diagram tools through a public, hosted MCP server. You need OpenCode and an internet connection; no API key, local server, or `.env` is required.

## Setup and check

1. Launch `opencode` from this folder, or copy the `mcp.servers.excalidraw` entry from `opencode.json` into your project's root `opencode.json` and launch OpenCode there.
2. From the configured folder or project, run `opencode mcp list` and look for **excalidraw: connected**. The OpenCode connection has not yet been verified.

Diagram contents are sent to the hosted service. `export_to_excalidraw` uploads a diagram and provides a shareable link; avoid sending sensitive diagrams. The interactive canvas may not render in every OpenCode interface.
