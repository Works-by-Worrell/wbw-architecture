# Works-by-Worrell: Agentic Governance Architecture for the Rest of Us
## A $0-Budget Case Study in AI Agent Orchestration, MCP Distribution, and Honest Engineering

**Author:** Roger A. Worrell (Hrothgar Warlock) — Principal Architect, Works-by-Worrell
**Date:** August 2026
**Status:** Draft

---

## 1. The Problem Statement

The AI agent tooling ecosystem has a class problem.

Enterprise organizations deploy AI coding assistants with dedicated platform teams, multi-million dollar cloud budgets, and custom infrastructure. Individual hobbyists run local scripts on a single machine. But there is a growing middle tier — **small distributed organizations (< 50 engineers)** — that need the governance rigor of the enterprise with the budget constraints of the hobbyist.

Works-by-Worrell is, in reality, a one-person engineering organization (though occasionally referred to in the plural when counting the various AI agents and personas operating within it). The architecture detailed here is designed and validated for teams of up to 50 engineers, but honestly tested by one — across multiple machines, multiple AI agent frameworks, and under a strict **$0.00/month infrastructure cost** constraint. Every pattern documented here was built to survive the addition of Engineer #2 through Engineer #50 without rearchitecting. The core engineering challenge:

> **How do you give every engineer in an async, multi-device, distributed org identical AI-governed tooling — with centralized governance, zero developer drift, and zero cloud compute charges?**

This is not a theoretical exercise. This document chronicles the real architecture, the real failure, and the real engineering pivot that answered this question.

---

## 2. The Platform: Works-by-Worrell

The platform is partitioned into specialized repositories, each with a single responsibility:

### Component Map

| Repository | Responsibility | Public/Private |
| :--- | :--- | :--- |
| **`warlock-mcp`** | MCP server — wraps org functions as AI tools (agent personas, user profiles, issue tracking, project evaluation) | Public |
| **`wbw-daemon`** | Orchestrator plugin & launcher — bootstraps agent identity, manages MCP lifecycle, enforces HitL execution gateway | Public |
| **`wbw-config`** | GitOps control plane — agent persona definitions, governed tool configurations, Definition of Ready policies | Public |
| **`wbw-config-private`** | Private configuration overlays — operator preferences, internal credentials, communication styling | Private |
| **`wbw-infra`** | Terraform IaC — GCP resource declarations, environment parameters, Workload Identity Federation | Private |
| **`eldritch-harvester`** | Autonomous data pipeline — public endpoint scraping, LLM-driven evaluation, YouTrack issue creation via MCP | Public |
| **`wbw-architecture`** | Architecture registry — ADRs, blueprints, devlogs, governance SOPs | Public |

### Why This Separation Matters

Each repository has an independent lifecycle, independent access controls, and can be open-sourced without exposing private configuration. An engineer cloning `wbw-daemon` gets the full orchestrator plugin without gaining access to internal profiles, credentials, or infrastructure declarations.

---

## 3. The Governance Model

### 3.1 Layered Identity Architecture

Agent identity is assembled from multiple layers at boot time:

```
┌─────────────────────────────────────────────────────────────┐
│  Layer 3: Private Overlay (identity.local.md)               │
│  - Operator communication preferences                       │
│  - Internal API tokens & secrets                            │
│  - Organization-specific behavioral constraints             │
│  - .gitignored — never committed to source control          │
├─────────────────────────────────────────────────────────────┤
│  Layer 2: Governed Agent Persona (wbw-config/agents/*.md)   │
│  - Role definitions (Daemon, Spike, Torque, Clutch)         │
│  - Tool permissions & model assignments                     │
│  - Centrally governed via Git PR review                     │
├─────────────────────────────────────────────────────────────┤
│  Layer 1: Public Base Rules (rules/base.md)                 │
│  - Open-source fallback behavior                            │
│  - Generic operational workflow                             │
│  - Safe for public distribution                             │
└─────────────────────────────────────────────────────────────┘
```

This separation guarantees that open-source contributors can clone, run, and extend the daemon without gaining access to enterprise context. Private overlays are injected at runtime and hard-ignored by Git.

### 3.2 Human-in-the-Loop Execution Gateway

The agent swarm operates under a **dual-layer execution boundary**:

- **Autonomous Zone:** Subagents (Spike, Torque, Clutch) are fully authorized to create/edit files, run unit tests, format code, and create local Git commits without per-action approval. This eliminates micro-approval fatigue.
- **Gated Zone (Push Package Protocol):** Remote actions — `git push`, CI/CD triggers, PR creation, infrastructure deployments — require a single consolidated approval breakpoint via interactive prompt. One approval per package of work, not one per command.

This maps directly to [**Principle 0: Consent**](../manifesto/00-consent.md) from the 11 Principles Manifesto: *"The system never executes destructive commands without the Operator's explicit consent."*

### 3.3 Definition of Ready Enforcement

Before work is dispatched to the agent swarm, the orchestrator validates that a tracked issue exists (GitHub Issues or YouTrack) and meets the organization's Definition of Ready criteria. This prevents speculative coding and ensures every piece of work has traceability.

---

## 4. The Cloud Run Experiment & Failure

### The Original Architecture

To support multi-device, multi-engineer access to governed MCP tools, `warlock-mcp` was deployed as a Cloud Run service in GCP (`warlock-mcp-nprd`, `us-central1`). Engineers connected via a local `gcloud run services proxy` tunnel, bridged through an SSE (Server-Sent Events) transport layer.

```
Engineer's Machine                          Google Cloud
┌──────────────┐    localhost:8080    ┌────────────────────┐
│  agy CLI     │ ──── SSE Proxy ───► │  Cloud Run         │
│  mcp-bridge  │                     │  warlock-mcp-nprd  │
│  watchdog    │                     │  (FastMCP / SSE)   │
└──────────────┘                     └────────────────────┘
```

This architecture was presented — and repeatedly confirmed by the LLM assistant — as operating within Google Cloud's Free Tier at **$0.00/month**.

### What Broke

On August 14, 2026, during a debugging session to resolve SSE connection drops, four successive Terraform changes were applied over 90 minutes:

1. `min_instance_count: 0 → 1` (eliminate cold-start connection drops)
2. `max_instance_count: 3 → 1` (force session affinity)
3. `max_instance_count: 1 → 5` (combat HTTP 429 rate limiting)
4. `cpu: 1 → 2, memory: 1Gi → 2Gi` (resolve resource exhaustion)

At each step, the human operator asked: *"What does this do to my budget?"*
At each step, the LLM responded: *"Still within free tier. $0.00."*

**This was incorrect.** Setting `min_instances = 1` with 2 vCPU / 2 GiB RAM creates a continuously warm instance that bills 24/7. The actual compute usage exceeded the Cloud Run free tier by over 2,000%.

### Why the LLM Got It Wrong

1. **Static Classification:** The LLM classified Cloud Run as "free tier eligible" based on product category knowledge, without performing the actual billing arithmetic.
2. **Symptom Patching:** Each configuration change was evaluated in isolation to fix the immediate error, without re-evaluating the cumulative financial impact.
3. **Nuance Failure:** CPU throttling reduces idle CPU billing but does **not** eliminate memory billing. The LLM failed to distinguish these billing dimensions.

### The Cascading Failure

Compounding the billing issue, the daemon launcher contained an unbounded retry loop with no timeout. When Cloud Run returned HTTP 429, this loop generated ~120 requests/minute indefinitely, maintaining container load and perpetuating the rate limit. Multiple orphaned watchdog processes amplified the problem.

### Financial Impact

| Category | Charge | Root Cause |
| :--- | :--- | :--- |
| Cloud Run | $9.65 | Warm instance billing (2 vCPU / 2 GiB, 24/7) |
| Gemini API | $7.06 | Surcharged `google_search` grounding tool (separate issue) |
| **Total** | **$16.71** | **1,111% increase over prior billing period** |

### A Note on Economic Gatekeeping

The Works-by-Worrell manifesto explicitly identifies economic gatekeeping as a failure of modern engineering culture (see [Principle 1: Radical Inclusion](../../manifesto/01-radical-inclusion.md)).

The MCP ecosystem reproduces this pattern in a new form. The protocol's current distribution model presents small organizations with a false binary:

- **Pay the cloud tax** ($10–30+/month) for a governed, distributed MCP server with centralized identity and policy enforcement.
- **Run ungoverned local scripts** ($0) with no organizational control, no version pinning, and no tamper protection.

This is the same *"Fuck you, I got mine"* dynamic from the principle — except the gatekeeper isn't a senior engineer hoarding Confluence passwords. It's an invisible infrastructure paywall enforced by serverless billing models that punish persistent connections.

For an independent developer managing high living costs, an unexpected $20 infrastructure charge isn't just an annoyance—it's a breach of trust. It shifts the dynamic from "safe experimentation" to "unbounded financial liability." The tooling ecosystem that claims to democratize AI-assisted development has, in practice, erected unpredictable paywalls that punish the exact kind of tinkering Radical Inclusion demands we welcome.

The `uvx` architectural pivot is the equivalent of the Greeters bell at the Burn gate: it tears down the economic barrier and delivers governed, immutable, centrally-controlled AI tooling at $0.00 — Day 1, any machine, any engineer.

---

## 5. The Architectural Pivot

### The Realization

Three conclusions emerged from the incident:

1. **Distributed MCP over serverless infrastructure is structurally incompatible with $0 budgets.** The MCP protocol's SSE transport requires persistent connections. Serverless platforms are designed for short-lived, stateless requests. There is no configuration that provides both reliable SSE transport and $0 billing.

2. **Governance does not require a running server.** Centralized governance is a property of the **control plane** (Git repositories, signed packages, CI/CD pipelines), not the **execution plane** (where the code physically runs).

3. **A remote server proxying governed markdown files from Git is an architectural anti-pattern.** Half of the MCP tools were simply fetching configuration files from GitHub — an expensive middleman for a `git pull`.

### The New Architecture

```
CONTROL PLANE (Centralized — GitHub)
┌───────────────────────────────────────────────────┐
│  wbw-config          → Agent personas, policies   │
│  wbw-config-private  → Private overlays           │
│  GitHub Packages     → warlock-mcp wheel (v0.0.x) │
│  GitHub Actions      → CI/CD build & publish      │
└───────────────────────────────────────────────────┘
                        │
                        │ git pull / uvx install (cached)
                        ▼
EXECUTION PLANE (Local — Any Engineer Workstation)
┌───────────────────────────────────────────────────┐
│  agy CLI → uvx warlock-mcp==0.0.10 --transport stdio │
│                                                   │
│  • Zero cloud infrastructure                      │
│  • Zero network latency                           │
│  • Impossible for GCP to generate a bill          │
└───────────────────────────────────────────────────┘
```

**Key changes:**
- `warlock-mcp` runs as a **local STDIO subprocess**, not a remote HTTP server.
- Distribution uses **`uvx` version-pinned packages** — immutable, pre-compiled Python wheels published by central CI/CD. Engineers cannot edit the source code. No developer drift. No pencil-whipping.
- Version upgrades propagate via a single pin change in `mcp_config.json`, governed through `wbw-config` Git PR review.
- **Deleted entirely:** Cloud Run service, DNS mappings, proxy watchdog scripts, SSE bridge processes, IAM bindings.

### Scaling Ceiling

| Org Size | Infrastructure Cost | Limiting Factor |
| :--- | :--- | :--- |
| **< 50** | $0.00 | None (GitHub free tier covers all) |
| **50–100** | ~$4/user/month | GitHub Teams for CODEOWNERS enforcement |
| **100–200** | ~$4/user/month | Per-user API tokens for rate limit headroom |
| **200+** | Varies | Swap GitHub Packages for internal Artifactory |

The architectural pattern scales indefinitely. Only the package registry changes at enterprise scale.

---

## 6. Industry Implications

### 6.1 The MCP Distribution Gap

As of August 2026, the MCP ecosystem has no standardized, cost-free solution for distributing governed MCP servers to distributed teams. The protocol's reliance on persistent connections (SSE, WebSocket) creates a structural incompatibility with $0 serverless platforms. This forces small organizations into one of two bad choices:

- **Local unmanaged source checkouts** → governance drift
- **Cloud-hosted remote servers** → recurring infrastructure cost

The `uvx` immutable package pattern demonstrated here is a third path that eliminates both problems, but it is not yet a standard industry practice.

### 6.2 LLM Financial Accountability

LLMs cannot be trusted as cloud infrastructure financial auditors. When an LLM says a configuration is "free," it is classifying by product category, not computing billing formulas. Organizations using AI assistants to configure cloud infrastructure should treat all cost assertions as unverified claims requiring manual arithmetic validation.

### 6.3 Governance Is a Control Plane Property

The most expensive lesson from this project: **running a $10/month cloud server to centralize governance is solving the wrong problem.** Governance is enforced by who controls the package registry, the Git repository, and the CI/CD pipeline — not by where the code executes at runtime.

---

## 7. Supporting References

### Foundational Framework
- [The 11 Principles of Software Engineering Manifesto](../manifesto/README.md) — The philosophical and architectural framework underpinning WBW governance
- [Principle 0: Consent](../manifesto/00-consent.md) — Human-in-the-Loop architecture, scoped sandboxes, execution boundaries

### Architecture Records
- [ADR 0002: Repository Split & GitOps Config Separation](../../wbw-architecture/adrs/0002-repository-split-and-gitops-config-separation.md)
- [ADR 0004: GitOps Agent Governance Datastore](../../wbw-architecture/adrs/0004-gitops-agent-governance-datastore.md)
- [ADR 0005: WBW Daemon Plugin Architecture](../../wbw-architecture/adrs/0005-wbw-daemon-plugin-architecture.md)
- [Blueprint 0001: Cloud Migration](../../wbw-architecture/blueprints/0001-cloud-migration-blueprint.md)
- [Blueprint 0003: Eldritch Harvester Pipeline](../../wbw-architecture/blueprints/0003-eldritch-harvester-pipeline.md)

### Incident Documentation
- [Cloud Run MCP Post-Mortem](./cloud-run-mcp-post-mortem.md) — Detailed incident timeline, root cause analysis, and remediation

### Invalidated Documents (Pending Revision)
- [Blueprint 0006: Cloud-Native Agent Architecture Presentation](../../wbw-architecture/blueprints/0006-cloud-native-agent-architecture-presentation.md) — Cloud Run MCP claims invalidated
- [Cost & Governance Case Study](../../wbw-daemon/docs/architecture_cost_case_study.md) — "$0.00 warm instance" claim invalidated
