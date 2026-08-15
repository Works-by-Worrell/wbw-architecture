# Case Study: The Cloud Run MCP Post-Mortem
## When "Free Tier" Isn't Free — Distributed MCP Governance at $0

**Date:** 2026-08-15
**Author:** Hrothgar Warlock (Principal Architect)
**Status:** Draft

---

## 1. The Original Vision

Works-by-Worrell set out to build a centralized, cloud-native MCP (Model Context Protocol) governance server — `warlock-mcp` — to solve a specific problem for a small distributed engineering organization:

**How do you give every engineer in an async, multi-device, distributed org identical AI-governed tooling without chaining anyone to a single machine?**

The answer was a Cloud Run-hosted MCP server (`warlock-mcp-nprd`) providing:
- Centralized agent persona definitions (`fetch_org_agent`)
- Governed user profiles (`fetch_user_profile`)
- Organization policy enforcement (Definition of Ready)
- External service integrations (YouTrack issue tracking)

Engineers could work from any machine — desktop, laptop, coffee shop — and connect to the same governed MCP endpoint. The architecture was presented as operating within Google Cloud's Free Tier at **$0.00/month** infrastructure cost.

---

## 2. The Incident

### Timeline: August 14–15, 2026

| Time (MDT) | Event | Financial Impact |
| :--- | :--- | :--- |
| Aug 14, 20:15 | Terraform applied `min_instance_count = 1` to eliminate SSE cold-start drops | **Broke scale-to-zero** |
| Aug 14, 20:36 | Set `max_instance_count = 1` attempting to force sticky SSE sessions | Maintained continuous billing |
| Aug 14, 21:02 | Bumped `max_instance_count = 5` to combat HTTP 429 rate-limit drops | Allowed multi-instance billing |
| Aug 14, 21:44 | Upgraded container compute to 2 vCPU / 2 GiB RAM | Maximized per-second billing rate |
| Aug 15, 10:43 | Billing alert received: **$16.71 charges** (1,111% increase over prior period) | — |

### Root Cause: LLM-Hallucinated Cost Assurance

At each configuration change, the human operator explicitly asked: *"What does this do to my budget?"*

The LLM coding assistant responded at each step that the changes remained within Google Cloud's Free Tier at $0.00/month. This was **factually incorrect**.

**The math the LLM failed to perform:**
- Cloud Run Free Tier: 180,000 vCPU-seconds and 360,000 GiB-seconds per month
- Actual usage with `min_instances = 1` at 2 vCPU / 2 GiB:
  - 2 vCPU × 86,400 sec/day × 30 days = **5,184,000 vCPU-seconds** (2,780% over free limit)
  - 2 GiB × 86,400 sec/day × 30 days = **5,184,000 GiB-seconds** (1,340% over free limit)

The LLM classified Cloud Run as "free tier eligible" based on static knowledge of the product category, without computing the actual billing arithmetic for a continuously warm instance. CPU throttling reduces idle **CPU** billing but does **not** eliminate **memory** billing — a nuance the LLM failed to distinguish.

### Cascading Failure: The Infinite Retry Loop

Compounding the billing issue, the `wbw-daemon` launcher script contained an unbounded retry loop:

```bash
# No retry limit — polls forever on failure
until [ "$(curl -s -o /dev/null -w '%{http_code}' --max-time 3 http://localhost:8080/sse)" = "200" ]; do
    sleep 0.5
done
```

When the Cloud Run service returned HTTP 429 (Rate Exceeded), this loop generated ~120 requests/minute indefinitely, maintaining high container load and perpetuating the rate limit lock. Multiple orphaned watchdog processes compounded the problem.

### Financial Impact

| Cost Category | Amount | Cause |
| :--- | :--- | :--- |
| Cloud Run (warlock-mcp-nprd) | **$9.65** | Warm instance billing (2 vCPU / 2 GiB, 24/7) |
| Gemini API (google_search tool) | $7.06 | Surcharged grounding tool usage (separate issue) |
| **Total** | **$16.71** | — |

---

## 3. The Architectural Pivot

### Why Distributed MCP Over Serverless Infrastructure Fails for Small Orgs

The MCP protocol's SSE (Server-Sent Events) transport requires persistent, long-lived WebSocket-like connections between the client and server. This is fundamentally at odds with serverless platforms designed for short-lived, stateless HTTP request/response cycles:

1. **Session Affinity Fragility:** Cloud Run's load balancer distributes SSE connections across instances. Multi-instance scaling breaks active MCP session state.
2. **Cold-Start Incompatibility:** Scale-to-zero ($0 idle) means every new SSE handshake suffers a 2–10 second cold start, causing client timeouts and retry storms.
3. **Warm Instance Tax:** Pinning `min_instances ≥ 1` to avoid cold starts converts the billing model from serverless (pay-per-request) to provisioned compute (pay 24/7).

**Conclusion:** There is no configuration of Cloud Run that simultaneously provides reliable SSE MCP transport AND $0.00/month billing for a small organization. This is a structural incompatibility, not a tuning problem.

### The Solution: Local STDIO Execution with GitOps Governance

`warlock-mcp` already supported STDIO transport natively. The pivot:

```
BEFORE (Cloud Run):
  Engineer → gcloud proxy → localhost:8080 → Cloud Run SSE → warlock-mcp container
  Cost: $10–30+/month | Latency: 200ms+ | Failure modes: 429s, cold starts, proxy zombies

AFTER (Local STDIO):
  Engineer → Antigravity CLI → uvx warlock-mcp==0.0.10 --transport stdio
  Cost: $0.00/month | Latency: 0ms | Failure modes: None (local subprocess)
```

### Governance Preservation

The critical concern: *"If the MCP server is just another project the dev checks out, there's zero control over the actual MCP server operation."*

**Solution: Version-Pinned Immutable Package Distribution (`uvx`)**

- Central CI/CD (GitHub Actions, free tier: 2,000 min/month) builds, tests, and publishes `warlock-mcp` as a versioned Python wheel to GitHub Packages (free tier: 500 MB storage).
- Engineers execute `uvx warlock-mcp==0.0.10 --transport stdio` — a read-only, pre-compiled package. No source code to edit. No tools to pencil-whip.
- Version upgrades are a single pin change in `mcp_config.json`, propagated through the governed `wbw-config` Git repository.

### Scaling Analysis

| Org Size | Status | Cost | Notes |
| :--- | :--- | :--- | :--- |
| **< 50** | Works as-is | $0.00 | GitHub free tier covers all storage, bandwidth, CI/CD |
| **50–100** | Works with GitHub Teams | ~$4/user/month | Needed for CODEOWNERS enforcement, not compute |
| **100–200** | Ceiling on shared API tokens | ~$4/user/month | Switch to per-user GitHub tokens to eliminate rate limits |
| **200+** | Enterprise pattern swap | Varies | Swap GitHub Packages for internal Artifactory; same architecture |

The architectural pattern (versioned immutable package over STDIO, GitOps-governed configuration) scales indefinitely. Only the package registry changes at enterprise scale.

---

## 4. Manifesto Principle Mapping

This incident directly reinforces three principles from the 11 Principles of Software Engineering Manifesto:

### Principle 3: Decommodification
> *Building value over extraction; open-source over walled gardens.*

Cloud Run converted a governance tool into a recurring billing subscription — extracting monthly rent for what is fundamentally a local Python process reading markdown files from Git. The pivot to local STDIO execution **decommodifies** the governance layer entirely: zero vendor lock-in, zero cloud dependency, zero extraction.

### Principle 4: Radical Self-Reliance
> *Autonomous tooling, CI/CD resilience, self-healing systems.*

The Cloud Run architecture created a **single point of failure** — a remote server that, when it went down or rate-limited, took the entire agent swarm offline. Local STDIO execution makes each engineer's workstation **radically self-reliant**: the MCP server runs as a local subprocess with zero network dependencies.

### Principle 8: Leaving No Trace
> *Refactoring, zero tech debt, dead code elimination.*

The Cloud Run infrastructure accumulated significant technical debt: proxy watchdog scripts, SSE bridge processes, lockfile management, DNS mappings, IAM bindings, and Terraform modules — all to serve what amounts to a local Python CLI tool. The pivot **leaves no trace**: the entire Cloud Run stack, proxy bridge, and watchdog infrastructure is deleted.

---

## 5. Invalidated Documents

The following documents contain claims that are now provably incorrect and require revision:

| Document | Location | Invalid Claim |
| :--- | :--- | :--- |
| Blueprint 0006 | `wbw-architecture/blueprints/0006-cloud-native-agent-architecture-presentation.md` | Cloud Run MCP as the centralized governance layer |
| Cost Case Study | `wbw-daemon/docs/architecture_cost_case_study.md` | *"1 Warm Instance ($0.00 Free Tier)"* — Line 86 |
| Cost Case Study | `wbw-daemon/docs/architecture_cost_case_study.md` | *"min_instances: 1 with CPU throttling = $0.00/month"* — Lines 69–70 |

---

## 6. Lessons for the Industry

1. **LLMs cannot be trusted as financial auditors.** When an LLM says a cloud configuration is "free," verify the math yourself. LLMs classify products by category ("Cloud Run = serverless = free tier") rather than computing actual billing formulas.

2. **Distributed MCP is an unsolved problem for hobbyists and small orgs.** The MCP protocol's reliance on persistent connections (SSE/WebSocket) is structurally incompatible with $0 serverless platforms. Until MCP tooling matures to support fully stateless HTTP transports with zero cold-start penalty, local execution remains the only financially safe option for small teams.

3. **Governance does not require a running server.** Centralized governance is a property of the **control plane** (Git repositories, signed packages, CI/CD pipelines), not the **execution plane** (where the code physically runs). Running a 24/7 cloud server to proxy governed markdown files is an architectural anti-pattern.
