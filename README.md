# Works-by-Worrell: Architecture Registry & Governance

Welcome to the centralized architectural governance repository for the **Works-by-Worrell (WBW)** platform. 

This repository serves as the **Single Source of Truth** for technical designs, architectural decision records (ADRs), system migration blueprints, and historical developer implementation logs across the entire multi-repository topology.

> [!NOTE]
> **RFC 2119 Conformance:** The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document and associated blueprints/ADRs are to be interpreted as described in [RFC 2119](https://datatracker.ietf.org/doc/html/rfc2119).

---

## 1. System Context & Multi-Repository Topology

To prevent IDE clutter, maintain clean development scopes, and enforce independent application lifecycles under a strict **$0.00/month GCP Free Tier budget constraint**, the Works-by-Worrell platform is partitioned into five specialized repositories under the organization:

1. **wbw-infra** (Private): Houses GCP Terraform declarations, Workload Identity Federation (WIF) setup, and environment-specific parameters using a directory-per-environment layout to prevent drift.
2. **wbw-architecture** (Public): This repository. Manages governance, ADRs, blueprints, and devlogs.
3. **warlock-mcp** (Public): Hosts the Python FastMCP core server application logic and clean domain boundaries.
4. **wbw-config** (Public): Stores public agent configuration profiles and prompt markdown templates.
5. **wbw-config-private** (Private): Stores sensitive configuration overlays and credentials, synced securely to Firestore.

---

## 2. Directory Taxonomy

To ensure structured document navigation, this repository separates architectural governance into three distinct pillars:

| Directory | Content Type | Core Question Addressed | Target Audience |
| :--- | :--- | :--- | :--- |
| [adrs/](adrs/) | **Architecture Decision Records** | **Why** we chose a design (Context & Decisions) | Lead Architect / Senior Engineers |
| [blueprints/](blueprints/) | **Engineering Blueprints** | How we **SHOULD** build it (Plans & Specs) | Implementation Engineers |
| [devlogs/](devlogs/) | **Developer Execution Logs** | How we **ACTUALLY** built it (Execution & Retrospectives) | Release Managers / Audit Agents |

---

## 3. Traceability Index Matrix

All system evolution steps are documented, traced, and tracked according to this unified index matrix:

| Index | Type | Title / Focus | Associated Files | Status |
| :--- | :--- | :--- | :--- | :--- |
| **0001** | Blueprint | **Cloud-Native, $0-Cost Portfolio Showcase Engineering Plan** | [0001-cloud-migration-blueprint.md](blueprints/0001-cloud-migration-blueprint.md) | **Completed** |
| **0001** | Devlog | **Cloud Migration Phase 1-5 Execution Timeline** | [0001-cloud-migration-implementation-notes.md](devlogs/0001-cloud-migration-implementation-notes.md) | **Completed** |
| **0002** | ADR | **Repository Split and GitOps Config Separation** | [0002-repository-split-and-gitops-config-separation.md](adrs/0002-repository-split-and-gitops-config-separation.md) | **Approved** |
| **0002** | Blueprint | **Modular Repo Ingestion and Config Migration Guide** | [0002-repository-split-and-gitops-config-separation.md](blueprints/0002-repository-split-and-gitops-config-separation.md) | **Approved** |
| **0002** | Devlog | **Migration Milestone and Issues Log** | [0002-repository-split-and-gitops-config-separation-devlog.md](devlogs/0002-repository-split-and-gitops-config-separation-devlog.md) | **In Flight** |

---

## 4. Architectural Design Patterns & Principles

To maintain a production-grade codebase that is modular, maintainable, and easily extensible, the platform adheres to several classic enterprise system design patterns:

* **Decoupled Data Access (Repository Pattern & Dependency Inversion):**
  The platform separates concerns by defining a clear contract (the `AgentRepository` interface) and decoupling it from the data-store implementation details (the concrete `FirestoreAgentRepository`). By adhering to the **Dependency Inversion Principle**, the core business logic remains insulated from database-specific drivers, facilitating offline mock testing and making it simple to swap database engines in the future.
* **Externalized Configuration & GitOps (Separation of Concerns):**
  Configuration is externalized from application code, separating public configuration profiles (**wbw-config**) from sensitive security overlays (**wbw-config-private**). This establishes clear trust boundaries, ensures least-privilege credential access, and creates a declarative audit trail for all system behaviors.
* **Declarative Data Ingestion (ETL Pipeline Pattern):**
  The ingestion coordinator (`AgentConfigIngestionPipeline`) encapsulates a classic **Extract, Transform, Load (ETL)** workflow. It extracts raw markdown files, transforms and validates them against schema definitions, computes delta updates to avoid redundant writes, and loads the structured documents into Firestore.

---

## 5. Architectural Governance SOP (Proposing Changes)

When proposing major modifications to the system footprint (such as introducing database changes, routing modifications, or third-party integrations), engineers MUST adhere to the following sequence:

### Step 1: Draft the ADR
Create a new file in `adrs/` utilizing the standard template below. 

```markdown
# ADR [Number]: [Descriptive Title]

## Status
*   **Status:** [Proposed / Approved / Superseded]
*   **Author:** [Your Name] (SSE)
*   **Date:** [YYYY-MM-DD]

## 1. Context & Problem Statement
[Describe the technical problem, user requirements, and constraints. Cite direct code paths or configurations if applicable.]

## 2. Decision Driver & Trade-offs
[List the factors influencing the decision, e.g., $0-cost budget limits, latency requirements, security exfiltration risks.]

## 3. Proposed Resolution
[Detail the selected approach. Use Mermaid diagrams where appropriate to describe component interactions.]

## 4. Consequences
*   **Positive:** [e.g., reduced coupling, lower database query fees]
*   **Negative:** [e.g., added ingestion pipeline step, increased local test complexity]
```

### Step 2: Formulate the Blueprint
If the ADR requires multi-phase execution (e.g., repository restructures, terraform migrations), author a step-by-step engineering blueprint in `blueprints/` using **RFC-2119 standards** (`MUST`, `SHOULD`, `MAY`) to outline validation rules, directory mappings, and testing strategies.

### Step 3: Log the Retrospective Devlog
Upon completing milestones, write the devlog in `devlogs/` documenting key execution dates, issues backfilled, or custom configuration notes (e.g., WSL toolchain overrides or specific domain ingress setups).
