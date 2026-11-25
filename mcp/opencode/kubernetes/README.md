# Kubernetes MCP

This local MCP lets OpenCode inspect Kubernetes clusters using read-only tools. It reaches the Kubernetes API over your local network or VPN using your kubeconfig and any required local cloud-auth helper; nothing is installed in the cluster.

## Setup

1. Install OpenCode, Node.js 18+ with `npx`, and `kubectl`. Install Helm only if you need Helm features. If the cloud API endpoint is private, connect to a network that can reach it (for example, your VPN). Sign in with any cloud authentication helper your kubeconfig uses.
2. Set `KUBE_DEV_CONFIG` to an **absolute, readable kubeconfig file path**, not a Kubernetes context name. This folder's single `kubernetes` server passes that value as `KUBECONFIG`. You can export it in your shell, or create a local `.env` in this folder. Example `.env` (replace this fictional path with your own):

   ```dotenv
   KUBE_DEV_CONFIG=/path/to/example/dev.kubeconfig
   ```

   Or, without `.env`, run `export KUBE_DEV_CONFIG=/path/to/example/dev.kubeconfig` in the launching shell. Replace the example path before use.

3. From the repository root, use the **same shell** for the environment check and OpenCode:

   ```sh
   cd mcp/opencode/kubernetes
   # If a local .env exists, load it into this shell first:
   if [ -f ./.env ]; then set -a; . ./.env; set +a; fi
   if [ -n "${KUBE_DEV_CONFIG:-}" ] &&
      [ "${KUBE_DEV_CONFIG#/}" != "$KUBE_DEV_CONFIG" ] &&
      [ -f "$KUBE_DEV_CONFIG" ] && [ -r "$KUBE_DEV_CONFIG" ]; then
     kubectl --kubeconfig="$KUBE_DEV_CONFIG" config get-contexts &&
     kubectl --kubeconfig="$KUBE_DEV_CONFIG" config current-context &&
     kubectl --kubeconfig="$KUBE_DEV_CONFIG" -n default get pods &&
     opencode mcp list &&
     opencode
   else
     printf 'Set KUBE_DEV_CONFIG to an absolute, readable kubeconfig file.\n' >&2
   fi
   ```

   If your read access is limited to another namespace or resource, use that for the `kubectl` read test. Check that `kubernetes` shows **connected** in `opencode mcp list`, then ask OpenCode to list pods in an authorized namespace with `kubectl_get`. A connected MCP alone does not prove cluster access; reads still depend on Kubernetes RBAC. Live access has not been tested here.

OpenCode does not load `.env` automatically. Reloading configuration alone cannot add missing environment variables; after changing them, you may need a new OpenCode process. If `KUBE_DEV_CONFIG` is empty, the MCP may silently fall back to a default kubeconfig; do not skip the prelaunch check. Inherited `KUBECONFIG_PATH`, `KUBECONFIG_YAML`, `KUBECONFIG_JSON`, or `K8S_SERVER` together with `K8S_TOKEN` can override the intended file. Remove conflicting inherited settings before launching.

## Multiple clusters

- **Dev and prod contexts in one file:** `kubectl --kubeconfig="$KUBE_DEV_CONFIG" config get-contexts` lists the available context names. Ask for an explicit context on each `kubectl_get` query (for example, `dev` or `prod`) rather than changing the global current context. A context is a name *inside* a kubeconfig, not the path to that file. Use only contexts your credentials can read.
- **Two separate kubeconfig files:** In your target project's `opencode.json`, merge the following two entries under `mcp.servers` without replacing unrelated settings. This is an example for another project; this folder still configures only `kubernetes`. Set `KUBE_DEV_CONFIG` and `KUBE_PROD_CONFIG` to separate absolute, readable files in the shell launching OpenCode, and check **both** before launch.

  ```json
  {
    "mcp": {
      "servers": {
        "kubernetes-dev": {
          "type": "local",
          "command": ["npx", "-y", "mcp-server-kubernetes@4.2.3"],
          "environment": {
            "ALLOW_ONLY_READONLY_TOOLS": "true",
            "KUBECONFIG": "{env:KUBE_DEV_CONFIG}"
          }
        },
        "kubernetes-prod": {
          "type": "local",
          "command": ["npx", "-y", "mcp-server-kubernetes@4.2.3"],
          "environment": {
            "ALLOW_ONLY_READONLY_TOOLS": "true",
            "KUBECONFIG": "{env:KUBE_PROD_CONFIG}"
          }
        }
      }
    }
  }
  ```

  With an optional local `.env`, add a fictional example line `KUBE_PROD_CONFIG=/path/to/example/prod.kubeconfig`, then load it in the target project's launching shell as above. Otherwise, export both variables in that shell. Replace all example paths. Before launching the two-server setup, check both variables:

  ```sh
  check_kubeconfig() {
    [ -n "$1" ] && [ "${1#/}" != "$1" ] && [ -f "$1" ] && [ -r "$1" ]
  }
  if check_kubeconfig "${KUBE_DEV_CONFIG:-}" &&
     check_kubeconfig "${KUBE_PROD_CONFIG:-}"; then
    opencode mcp list
    opencode
  else
    printf 'Set both KUBE_DEV_CONFIG and KUBE_PROD_CONFIG to absolute, readable kubeconfig files.\n' >&2
  fi
  ```

  Check that **both** named servers connect, and select the intended server and context in each read request. Keep credentials limited to read access.
