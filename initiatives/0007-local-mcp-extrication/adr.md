# ADR 0007: Local STDIO MCP Extrication Strategy

## Status
*   **Status:** Implemented
*   **Author:** Roger Worrell
*   **Date:** 2026-08-15

## 1. Context & Problem Statement
The integration of the Model Context Protocol (MCP) into the Warlock server (ADR 0006) utilized Server-Sent Events (SSE) to facilitate communication between the Cloud Run backend and the distributed agent runners (Eldritch Harvester, Wbw-Daemon). 

However, Cloud Run billing is based on instance uptime. SSE requires persistent, long-lived HTTP connections to stream context. As a result, the Cloud Run container could not scale to zero, accruing a 1,111% increase in expected billing over a 30-day period. This violates the core $0.00 infrastructure cost constraint of the Works-by-Worrell organization and exposes developers to unbounded financial liability (an unacceptable form of Economic Gatekeeping).

## 2. Decision Driver & Trade-offs
*   **Budget:** Must strictly enforce the $0.00 infrastructure cost limit.
*   **Protocol Constraints:** MCP requires either persistent SSE (HTTP) or STDIO (Subprocess) transport.
*   **Governance:** We must maintain central governance and zero-drift deployment of the MCP server, even without a cloud backend.

## 3. Resolution
**Demolish the Cloud Run architecture and standardise on immutable, local `uvx` package execution via STDIO.**

Instead of deploying `warlock-mcp` to a cloud server, it will be published and distributed as a strictly version-pinned Python package. Any client requiring MCP tools (e.g., Harvester, Daemon) will execute the server locally as a subprocess using `uvx run warlock-mcp --transport stdio`.

## 4. Consequences
*   **Positive:** **Immediate $0.00 infrastructure cost.** The system is structurally immune to cloud billing traps because there is no cloud compute.
*   **Positive:** Retains strict enterprise governance. Because clients use `uvx` to execute a specific, immutable version of the package, "developer drift" remains impossible.
*   **Positive:** Decreases latency by eliminating network hops for tool calls.
*   **Negative:** Shifts compute burden to the local client machine, though the overhead for tool routing is negligible compared to the LLM execution itself.
