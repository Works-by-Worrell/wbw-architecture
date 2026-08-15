# Blueprint 0004: WBW Daemon Plugin Scaffolding

## Goal
Implement a robust, OSS-safe Antigravity plugin (`wbw-daemon`) that securely tunnels to a Cloud Run MCP server and dynamically bootstraps proprietary operator identities without risking Git leakage.

## Architecture Highlights
- **Pre-flight Bootstrap:** A wrapper script fetches proprietary identity via an out-of-band MCP call before Antigravity boots.
- **Git Safety:** Proprietary payloads are written strictly to `rules/identity.local.md`, which is hard-ignored in Git.
- **Auto-Discovery:** Antigravity natively merges public fallback instructions (`rules/base.md`) with the proprietary local file.
- **Singleton Guard:** The `gcloud proxy` is fenced by a lock mechanism to prevent port collision and zombie processes.
- **Frictionless Install:** An `install.sh` script handles environment variable provisioning and workspace symlinking.

---

## Execution Segmentation

### 👤 The Mayor (Human Operator)
*High-level infrastructure, repository management, and validation.*

1. **Repository Creation:** Initialize the `Works-by-Worrell/wbw-daemon` repository on GitHub.
2. **Infrastructure Provisioning:** Ensure the Cloud Run Warlock MCP server is deployed and accessible.
3. **Environment Setup:** Ensure local `gcloud` CLI is authenticated and `AGY_OPERATOR_ID` logic is defined.
4. **End-to-End Validation:** Run the `install.sh` and execute the `wbw-daemon` bootstrap to verify the agent assumes the "Camp Lead" identity.

### 🤖 Spike (Agent Execution)
*Scaffolding, scripting, and structural implementation.*

1. **Directory Fencing:** Create the isolated working directory for the repository clone (e.g., `~/Source/WBW/wbw-daemon`).
2. **Plugin Manifests:** Write `plugin.json` and `mcp_config.json`.
3. **Installation Script:** Write `install.sh` to:
   - Prompt for / discover `$AGY_OPERATOR_ID` and `$AGY_WORKSPACE_ROOT`.
   - Symlink the plugin folder into `.agents/plugins/wbw-daemon`.
4. **MCP Bridge (`bin/mcp-bridge.sh`):**
   - Implement the `flock` / PID singleton guard for port 8080.
   - Execute `gcloud run services proxy`.
   - Launch `@modelcontextprotocol/server-remote` over stdio.
5. **Bootstrapper (`bin/wbw-daemon`):**
   - Implement the `gcloud auth print-access-token` guard.
   - Execute the headless MCP call to `fetch_org_agent`.
   - Output the payload to `rules/identity.local.md`.
   - Launch the `agy` CLI.
6. **Public Rules (`rules/base.md`):**
   - Write the generic fallback logic instructing the Operator to re-authenticate if MCP transport fails.
7. **Git Hygiene (`.gitignore`):**
   - Hard-ignore `*.local.md`, `.env`, and OS artifacts.
