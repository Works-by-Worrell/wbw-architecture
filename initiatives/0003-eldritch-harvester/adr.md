# ADR 0003: Decoupled Eldritch Harvester Pipeline

## Status
*   **Status:** Approved
*   **Author:** Roger Worrell (SSE)
*   **Date:** 2026-07-31

---

## 1. Context & Problem Statement

Currently, the data ingestion and evaluation script (`nightly_evaluator.py`) resides in the root of the Works-by-Worrell meta-workspace. While functional for prototyping, this architecture tightly couples the execution of the ingestion pipeline to the local developer workspace. 

As the volume of data sources increases, running these tasks synchronously within the main workspace introduces unnecessary noise, clutters the root directory with temporary data queues (`hopper.txt`), and prevents the pipeline from being deployed as a standalone, scheduled background service on secondary hardware.

## 2. Decision

We will extract the ingestion, queueing, and evaluation logic into a dedicated, isolated repository named `eldritch-harvester`.

### Key Implementation Details:
1.  **Repository Isolation:** The new repository will contain only the scripts and dependencies required for data extraction and LLM-based evaluation.
2.  **Dependency Management:** The project will utilize `uv` for fast, reproducible environment management, avoiding heavy containerization for this specific service to ensure it can run natively on edge hardware (e.g., local Windows scheduled tasks).
3.  **MCP Decoupling:** The ingestion engine will not hold direct credentials to downstream tracking systems. It will exclusively communicate with the centralized `warlock-mcp` server via `session.call_tool()` to persist evaluated payloads.

## 3. Consequences

### Positive
*   **Environment Isolation:** Removes scraping dependencies (`BeautifulSoup`, etc.) and queue files from the primary workspace root.
*   **Deployability:** The engine can be cloned and executed via cron or Windows Task Scheduler on arbitrary secondary hardware without requiring the full WBW monorepo setup.
*   **Security:** By relying on the Warlock MCP server for persistence, the ingestion engine operates with zero-trust; it does not need direct access to YouTrack API tokens, only the Warlock identity token.

### Negative
*   **Overhead:** Requires initializing and maintaining a new GitHub repository and CI/CD hygiene standards.
*   **Network Latency:** Communicating via the MCP server over SSE introduces slight network latency compared to direct API calls, though this is negligible for an asynchronous background task.
