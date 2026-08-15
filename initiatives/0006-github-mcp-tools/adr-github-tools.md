# ADR 0006: Centralize GitHub Context via Warlock MCP

## Status
Draft

## Context
Currently, agentic workflows relying on the local `gh` CLI to create issues or perform repository operations frequently misroute payloads when executing within a multi-repository workspace. Relying on hierarchical directory rules (e.g., dropping `AGENTS.md` mapping rules in every folder) is brittle, adds cognitive load, and can fail if the agent executes commands from an unexpected working directory. We need a reliable mechanism to ensure agents map their context to the correct organizational repository.

## Decision
1. **Deprecate Local CLI Dependence**: We will deprecate reliance on the local shell `gh` CLI for core tracking workflows.
2. **Centralized MCP Tools**: We will implement native GitHub API operations (e.g., `create_issue`, `get_repo_context`) directly within the centralized Warlock MCP Server. 
3. **Explicit Context Contracts**: The newly exposed MCP tools will require the agent to explicitly pass a repository mapping string or identifier as part of the tool payload, enforcing context resolution at the architectural boundary rather than the shell execution layer.

## Consequences
- **Positive**: Eliminates brittle directory-based repository routing and ensures highly reliable issue creation.
- **Positive**: Centralizes GitHub API interactions, allowing better auditability, telemetry, and potential enforcement of our Definition of Ready at the MCP layer.
- **Negative / Risk**: Increases the complexity and operational surface area of the Warlock MCP Server, requiring an implementation phase to port `gh` CLI logic into the MCP backend.
