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

To prevent the "40 Confluence pages" anti-pattern and ensure highly cohesive documentation, this repository organizes architectural governance into **Initiative Bundles** and a core Constitutional **Manifesto**.

| Directory | Content Type | Core Purpose | Target Audience |
| :--- | :--- | :--- | :--- |
| [manifesto/](manifesto/) | **11 Principles Manifesto** | The foundational philosophy and constitutional rules governing the entire organization. | All Engineers & Agents |
| [initiatives/](initiatives/) | **Architecture Decision Bundles** | Bounded context folders containing all decisions, blueprints, and logs for a single architectural sequence. | Lead Architect / Implementation Engineers |

### Inside an Initiative Bundle

Every `initiatives/000N-slug/` directory contains up to three file types:
1. **`adr.md` (Architecture Decision Record)**: **Why** we chose a design (Context & Decisions).
2. **`blueprint.md` (Engineering Blueprint)**: How we **SHOULD** build it (Plans & Specs).
3. **`devlog.md` (Developer Execution Log)**: How we **ACTUALLY** built it (Execution & Retrospectives).

---

## 3. Traceability Index Matrix

All system evolution steps are documented, traced, and tracked according to this unified index matrix:

| Index | Initiative | Associated Bundle | Status |
| :--- | :--- | :--- | :--- |
| **0001** | Cloud-Native Migration Strategy | [initiatives/0001-cloud-migration/](initiatives/0001-cloud-migration/) | **Completed** |
| **0002** | Repository Split and GitOps Config Separation | [initiatives/0002-repository-split/](initiatives/0002-repository-split/) | **Completed** |
| **0003** | Eldritch Harvester Pipeline | [initiatives/0003-eldritch-harvester/](initiatives/0003-eldritch-harvester/) | **Completed** |
| **0004** | GitOps Agent Governance Datastore | [initiatives/0004-governance-datastore/](initiatives/0004-governance-datastore/) | **Approved** |
| **0005** | WBW Daemon Plugin Architecture | [initiatives/0005-wbw-daemon/](initiatives/0005-wbw-daemon/) | **Completed** |
| **0006** | Cloud Run $0 Extrication Pivot | [initiatives/0006-cloud-run-mcp/](initiatives/0006-cloud-run-mcp/) | **Completed** |

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

### Step 1: Initialize the Bundle
Create a new directory under `initiatives/` named `000N-descriptive-slug/`.

### Step 2: Draft the ADR
Create an `adr.md` file within the bundle utilizing the standard template below. 

```markdown
# ADR [Number]: [Descriptive Title]

## Status
*   **Status:** [Proposed / Approved / Superseded]
*   **Author:** [Your Name] (SSE)
*   **Date:** [YYYY-MM-DD]

## 1. Context & Problem Statement
[Describe the technical problem, user requirements, and constraints. Cite direct code paths or configurations if applicable.]

## 2. Decision Driver & Trade-offs
[List the factors influencing the decision, e.g., $0-cost budget limits, latency requirements, security risks.]

## 3. Proposed Resolution
[Detail the selected approach. Use Mermaid diagrams where appropriate to describe component interactions.]

## 4. Consequences
*   **Positive:** [e.g., reduced coupling, lower database query fees]
*   **Negative:** [e.g., added ingestion pipeline step, increased local test complexity]
```

### Step 3: Formulate the Blueprint
If the ADR requires multi-phase execution (e.g., repository restructures, terraform migrations), author a `blueprint.md` in the bundle using **RFC-2119 standards** (`MUST`, `SHOULD`, `MAY`) to outline validation rules, directory mappings, and testing strategies.

### Step 4: Log the Retrospective Devlog
Upon completing milestones, write a `devlog.md` in the bundle documenting key execution dates, issues backfilled, or post-mortem failure analysis (e.g., the Cloud Run pivot).
