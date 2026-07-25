# Devlog: Repository Split and GitOps Config Separation (GitHub Issues Provisioning)

- **Date:** 2026-07-24
- **Status:** Approved
- **Scope:** Initial setup of tracking issues, milestones, and custom labels across all new modular repositories.

---

## 1. Milestones & Labels Applied

The custom metadata has been created and configured across all repositories to support tracking.

- **Milestone `Project Migration`** was created/ensured across:
  - `wbw-infra` (Milestone #1)
  - `wbw-architecture` (Milestone #1)
  - `warlock-mcp` (Milestone #1)
  - `wbw-config` (Milestone #1)
  - `wbw-config-private` (Milestone #1)
- **Standard Labels Configured**:
  - Tags: `Warlock`, `Stability`, `Exfiltration`
  - Priorities: `Priority: Minor`, `Priority: Normal`, `Priority: Major`, `Priority: Critical`, `Priority: Show-stopper`

---

## 2. Issue Mapping & Actions Taken

### 📂 Repository: `wbw-infra` (Infrastructure & IaC)
| Issue # | Action | Title | Milestone | Priority | Tags |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **#1** | **Updated** | `[wbw-infra] Migrate and Restructure Terraform Scripts` | Project Migration | Major | `Warlock`, `Stability` |
| **#2** | **Updated** | `[wbw-infra] Update and Standardize Cloud Components, Service Accounts, WIF, and Resource Optimizations` | Project Migration | Major | `Warlock`, `Stability` |
| **#3** | **Created** | `[wbw-infra] Write Repository README and Infrastructure Setup Documentation` | Project Migration | Normal | `Warlock`, `Stability` |

### 📂 Repository: `wbw-architecture` (Governance & Docs)
| Issue # | Action | Title | Milestone | Priority | Tags | Status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **#1** | **Updated** | `[wbw-architecture] Write Sandbox Project Migration ADR` | Project Migration | Normal | `Warlock`, `Stability` | **Closed** (Completed) |
| **#2** | **Updated** | `[wbw-architecture] Sandbox Project Migration Blueprint` | Project Migration | Normal | `Warlock`, `Stability` | **Closed** (Completed) |
| **#3** | **Updated** | `[wbw-architecture] Migrate Cloud Migration Blueprint and Decouple Devlogs` | Project Migration | Normal | `Warlock`, `Stability` | Open |
| **#4** | **Created** | `[wbw-architecture] Write Repository README and ADR Indexing Documentation` | Project Migration | Normal | `Warlock`, `Stability` | Open |

### 📂 Repository: `warlock-mcp` (FastMCP Core Application)
| Issue # | Action | Title | Milestone | Priority | Tags |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **#1** | **Created** | `[warlock-mcp] Implement Domain-Driven Design (DDD) Repository Pattern` | Project Migration | Major | `Warlock`, `Stability` |
| **#2** | **Created** | `[warlock-mcp] Enforce Domain Schema Validation via Pydantic` | Project Migration | Normal | `Warlock`, `Stability` |
| **#3** | **Created** | `[warlock-mcp] Refactor Sync CLI Utility with Delta-Syncing` | Project Migration | Major | `Warlock`, `Stability` |
| **#4** | **Created** | `[warlock-mcp] Package Companion Docker Container (warlock-mcp-syncer)` | Project Migration | Normal | `Warlock`, `Stability` |
| **#5** | **Created** | `[warlock-mcp] Implement GitHub Actions CI/CD Workflow` | Project Migration | Major | `Warlock`, `Stability` |
| **#6** | **Created** | `[warlock-mcp] Write Repository README and FastMCP Developer Documentation` | Project Migration | Normal | `Warlock`, `Stability` |

### 📂 Repository: `wbw-config` (Public Configuration)
| Issue # | Action | Title | Milestone | Priority | Tags |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **#1** | **Created** | `[wbw-config] Establish Public Configuration Repository and Sync Pipeline` | Project Migration | Major | `Warlock`, `Stability` |
| **#2** | **Created** | `[wbw-config] Write Repository README and Public Configuration Documentation` | Project Migration | Normal | `Warlock`, `Stability` |

### 📂 Repository: `wbw-config-private` (Private Configuration Overlay)
| Issue # | Action | Title | Milestone | Priority | Tags |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **#1** | **Created** | `[wbw-config-private] Establish Private Configuration Repository and GitOps Secret Pipeline` | Project Migration | Major | `Warlock`, `Stability`, `Exfiltration` |
| **#2** | **Created** | `[wbw-config-private] Write Repository README and Private Secrets Pipeline Documentation` | Project Migration | Normal | `Warlock`, `Stability` |

### 📂 Repository: `warlock-agents` (Legacy Monorepo Sandbox)
| Issue # | Action | Title | Milestone | Priority | Tags |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **#39** | **Created** | `[warlock-agents] Archive and Deprecate Legacy Monorepo` | None | Minor | `Warlock`, `Stability` |

---

## 3. Cross-Repository Dependency Strategy

All dependencies are defined in the **Technical Implementation Plan** and **Acceptance Criteria** of the individual issues:
- **`warlock-mcp` CI/CD Workflow** (#5) lists GCP Artifact Registry, Cloud Run services, and WIF configurations (from `wbw-infra` #2) as prerequisites.
- **`wbw-config`** (#1) and **`wbw-config-private`** (#1) workflows require:
  - WIF and syncer service account configurations (from `wbw-infra` #2).
  - The built and pushed `warlock-mcp-syncer` container image (from `warlock-mcp` #5).
- **`warlock-agents`** (#39) deprecation lists successful verification of the entire migration sequence as a prerequisite.
