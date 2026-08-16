# Initiative 0009: SDK Orchestrator Pivot (Shift #9)

## Context & The Wall

The `wbw-daemon` project was initially designed as a bash wrapper around the `agy` (Antigravity) CLI. The goal was to build a local, terminal-based Swarm Orchestrator (Daemon) that could govern workflows, spawn subagents, and dynamically route tasks using the Warlock MCP server.

During late-stage testing of the Human-in-the-Loop (HitL) execution pipeline, we encountered a hard blocker:
* The interactive mode of the `agy` CLI (`-i`) aggressively strips core MCP proxy tools (`call_mcp_tool`) from the agent's schema.
* This is an unmapped binary edge-case in the CLI. Because the Warlock server has 7 tools, the CLI lazy-loads them, requiring `call_mcp_tool` to execute them. Without `call_mcp_tool` in interactive mode, the Orchestrator is permanently blind to the MCP governance layer.
* Workarounds (like Headless Print mode chaining) break the intended UX of a stateful, interactive Command Center.

## The Architectural Decision

We are abandoning the bash wrapper approach for `wbw-daemon`. Attempting to force a pre-compiled chat binary to act as a highly specialized, stateful swarm orchestrator is brittle and fundamentally limited.

**We are pivoting `wbw-daemon` to be written entirely in Python, using the Antigravity Python SDK (`google-antigravity`).**

## Why the Python SDK?

1. **Programmatic Orchestration:** The SDK bypasses the CLI's interactive mode limitations. We manage the interactive loop ourselves in Python while running the agent execution engine "headlessly," ensuring full access to MCP proxies.
2. **Deep Integration:** `wbw-daemon` can now directly import `worksbyworrell-core` and `warlock-mcp` Python libraries. We no longer need to execute `python -c` inside bash scripts to fetch profile identities.
3. **Data Stream Interception:** The SDK exposes asynchronous streams for the agent's internal thoughts (`response.thoughts`) and tool executions (`response.tool_calls`). This allows us to build real-time observability, custom logging, and potential HTML dashboard broadcasting in the future.
4. **True Swarm Control:** We can programmatically instantiate subagents, explicitly map their tools, and route message payloads between them with precise state management.

## Action Items

1. **Deprecate Bash Wrapper:** Archive the `wbw-daemon/bin/wbw-daemon` bash scripts.
2. **Initialize SDK Environment:** Add `google-antigravity` as a dependency to the `wbw-daemon` PyProject.
3. **Rebuild the Loop:** Implement `google.antigravity.utils.interactive.run_interactive_loop` wrapped in our custom identity and MCP bridging logic.
4. **Validate the Boot Sequence:** Rerun the Daemon boot sequence using the new Python-driven Orchestrator to confirm MCP tool execution.

## Conclusion

This is Shift #9. We found the absolute limits of the CLI and broke it. We are graduating to the raw programmatic layer. Meat & Salt.
