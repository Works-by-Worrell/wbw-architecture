# ADR 004: GitOps as the Primary Datastore for Agent Governance

## Status
Accepted

## Context
The initial architecture for the Works by Worrell (WBW) Enterprise Agent Governance platform relied on a GitHub Actions CI/CD pipeline to synchronize Markdown configuration files (Agent definitions like `dyno.md` and User Profiles like `default.md`) from the `wbw-config-private` repository into a NoSQL Firestore database. 

However, during active development, we identified three critical flaws with this architecture:
1. **CI/CD Throttling:** Relying on free-tier GitHub hosted runners introduced unacceptable latency (up to 15+ minutes) in syncing configuration to Firestore, causing the active MCP server to operate on stale governance rules and profiles.
2. **Unnecessary ETL Complexity:** Extracting Markdown from Git, pushing it to Firestore, and pulling it back out via the MCP server added unnecessary ETL middleware and compute overhead for static configuration data.
3. **Cost Inefficiency:** Writing static configuration data to a NoSQL datastore incurs unnecessary write/read operation costs on GCP.

## Decision
We will bypass the Firestore database entirely for static Agent Governance rules and User Profiles. The legacy GitHub Action Firestore sync pipelines will be fully deprecated and removed.

Instead, the `warlock-mcp` server will use the GitHub REST API to read these Markdown definitions directly from the `main` branch of the `wbw-config-private` repository in real-time.

Firestore will be explicitly reserved for dynamic, user-generated data, application state, and agent telemetry.

## Consequences
### Positive
* **Real-time Sync:** Agent rules are immediately available to the MCP server the moment a commit merges to `main`.
* **Reduced Infrastructure Complexity:** Eliminates the fragile GitHub Action sync workflow.
* **Cost Efficiency:** Reduces Firestore read/write operations and leverages GitHub's free API limits for configuration delivery.
* **True GitOps:** Configuration changes inherit Git's native version control, rollback capabilities, and audit trails.

### Negative
* **GitHub API Limits:** The MCP server is now subject to GitHub's API rate limits (5,000 requests per hour per authenticated user). This is acceptable for the current scale.
* **Authentication Overhead:** The Cloud Run instance requires a securely injected GitHub Personal Access Token (PAT) via Secret Manager or environment variables to authenticate API calls to the private repository.
