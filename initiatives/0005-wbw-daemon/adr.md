# ADR 0005: WBW Daemon Plugin Architecture

## Status
Proposed

## Context
We need a consistent, reliable connection to the Cloud Run MCP server for agentic workflows. We evaluated multiple approaches and determined that encapsulating the connection logic within an Antigravity plugin is the optimal strategy. The plugin will provide the necessary tooling to securely proxy to Cloud Run and bootstrap the agent's identity context.

## Decision
1. **Repository & Naming Alignment**: We will create a new repository and plugin, both named `wbw-daemon`. Matching the repository name to the plugin namespace reduces cognitive load.
2. **Installation & Discovery**: The repository will be cloned into the standard workspace (e.g., `~/Source/WBW/wbw-daemon`). An `install.sh` script will run to prompt for/derive environment variables (`AGY_OPERATOR_ID`, `AGY_WORKSPACE_ROOT`), save them to a local config, and symlink the plugin into the workspace's `.agents/plugins/wbw-daemon` directory.
3. **Pre-flight Bootstrapping**: A wrapper script (`bin/wbw-daemon`) will execute a sidecar MCP call *before* launching Antigravity. This fetches the most recent Daemon agent profile (merging public and proprietary definitions) and writes it to `rules/instructions.md` so the agent boots with the correct context.
4. **Plugin Structure**: The plugin will adhere to the following architecture:
   - `plugin.json`: Registers the plugin namespace.
   - `mcp_config.json`: Declares the MCP server configuration at the plugin root.
   - `bin/mcp-bridge.sh`: A wrapper script executed by the MCP configuration. It will handle starting `gcloud run services proxy` and launching `@modelcontextprotocol/server-remote` over stdio.
5. **Proxy Singleton Guard**: `mcp-bridge.sh` will implement a lockfile/PID guard on its designated port to prevent multiple proxy instances from colliding or spawning zombie processes.
6. **Auth Expiration Mitigation**: The `wbw-daemon` startup script will check `gcloud auth print-access-token` before booting. Additionally, a public instruction in the plugin will direct the agent: *"If you encounter an MCP transport or connection error, instruct the Operator to run `gcloud auth login` and restart the session."*

## Consequences
- **Positive**: Seamless DevEx via `install.sh` handling environment variables and symlinking.
- **Positive**: Agent identity is strictly enforced at boot time without race conditions.
- **Positive**: Proxy collisions and auth expirations are explicitly guarded against and handled gracefully for the Operator.
- **Negative / Risk**: Requires maintaining the standalone sidecar script to query the MCP server outside of the Antigravity context.
