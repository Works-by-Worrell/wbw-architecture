# ADR 0001: Cloud-Native Migration Strategy (Deprecated)

## Status
*   **Status:** Deprecated (Superseded by ADR 0007)
*   **Author:** Roger Worrell
*   **Date:** 2026-07-15 (Reconstructed)

## 1. Context & Problem Statement
The Tailscale baseline (ADR 0000) failed to provide a reliable, frictionless execution environment for the open-source contribution agents. Managing local networking, Python virtual environments across distinct operating systems, and host-machine uptime limits velocity. We need a highly available, standardized execution plane that does not rely on a specific physical laptop or desktop being powered on and correctly configured.

## 2. Decision Driver & Trade-offs
*   **Availability:** Must be accessible 24/7 without managing local hardware states.
*   **Standardization:** The execution environment must be immutable (containerized) to eliminate "it works on my machine" drift.
*   **Budget:** Must adhere to the strict $0.00 infrastructure cost constraint.

## 3. Resolution
Migrate the agent execution layer to **Google Cloud Platform (GCP) Cloud Run**. 
Cloud Run provides a serverless, scale-to-zero container execution environment. Because it scales to zero when no agents are actively running workloads, it theoretically satisfies the $0.00 budget constraint using the GCP Free Tier. This also marks the deprecation of the `~/Source/WBW` monolith in favor of the structured `wbw-infra` repository to manage the cloud deployment via Terraform.

## 4. Consequences
*   **Positive:** Completely eliminates the Tailscale network and local hardware dependencies. 
*   **Positive:** Forces the containerization of the agent ecosystem, significantly improving architectural maturity.
*   **Negative (Fatal Trap):** Relies on the assumption that agent workloads (specifically the eventual integration of the MCP protocol over SSE) can reliably trigger the scale-to-zero mechanism. As documented in the Cloud Run post-mortem, persistent SSE connections fundamentally break serverless auto-scaling, leading to unbound billing liability. This architectural flaw eventually necessitates ADR 0007.
