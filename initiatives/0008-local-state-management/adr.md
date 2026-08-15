# ADR 0008: Local Filesystem State Management

## Status
Accepted

## Context
The `eldritch-harvester` pipeline was originally designed to synchronize its operational state (processed links, rejection logs, and cache data) to a Google Cloud Storage (GCS) bucket using a `GCSCacheManager`. This design anticipated a distributed execution environment where the Harvester might be run across multiple ephemeral machines (e.g., Cloud Run instances or GitHub Actions runners) requiring a centralized cloud bucket to prevent scraping duplicate data.

However, subsequent architectural decisions (see [ADR 0007](../0007-local-mcp-extrication/adr.md)) dictated a pivot to decentralized, local-edge execution to eliminate serverless billing constraints. In this new paradigm, the Harvester operates purely as a local CRON job on a centralized, dedicated edge node (a persistent laptop or server). 

By continuing to utilize GCS for state synchronization, the architecture unnecessarily maintained a tether to Google Cloud Platform (GCP). This introduced IAM complexity, required maintaining cloud service account keys in the local `.env` file, and violated the core objective of a strictly $0.00, cloud-agnostic execution environment.

## Organizational Learnings: The "Non-Existent Problem"
We must acknowledge that building a robust cloud-syncing cache manager was an attempt to solve a problem that did not exist. By over-indexing on horizontal scaling (running the Harvester across multiple machines), we introduced massive architectural friction to a tool that is fundamentally designed to execute chronologically on a single, centralized node.

However, the time spent decoupling the cognitive layer and establishing the Local MCP execution model (ADR 0007) yielded a massive, unintended financial payoff. Because we now rely on local tools exposed via MCP rather than cloud-native LLM plugins, we gain the ability to spawn local subagents (e.g., Dyno) that can execute web searches locally for free. This completely eliminates the exorbitant $0.035/call surcharge imposed by cloud LLM API grounding tools, cementing the value of the decentralized edge-execution architecture.

## Decision
We will entirely deprecate and remove the `GCSCacheManager` from the `eldritch-harvester` ecosystem. 

All operational state (processed links and rejection logs) will be written directly to the local filesystem (e.g., SQLite or flat files) of the edge node running the CRON job. The Harvester will no longer attempt to synchronize its state with any cloud provider.

## Consequences
- **Positive:** Complete elimination of GCP infrastructure dependencies for state management.
- **Positive:** Removal of GCP service account authentication requirements from the onboarding process, furthering the "Two-Syllable Execution" frictionless DX strategy.
- **Positive:** Faster execution times due to the elimination of network I/O for bucket synchronization.
- **Negative:** The Harvester can no longer be arbitrarily executed across multiple distinct physical machines without risking duplicate work, as state is now isolated to the node's local disk. (This is considered an acceptable trade-off, as the Harvester is fundamentally designed as a single-node background CRON daemon).
