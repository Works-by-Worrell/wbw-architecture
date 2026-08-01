# Devlog: Repository Split and GitOps Config Separation

- **Date:** 2026-07-25
- **Status:** Completed
- **Scope:** Phased execution of repository splits, terraform folder reorganization, OIDC setup, CI/CD automation, and developer hygiene controls.

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
  - Tags: `Warlock`, `Stability`, `Enhancement`
  - Priorities: `Priority: Minor`, `Priority: Normal`, `Priority: Major`, `Priority: Critical`, `Priority: Show-stopper`

---

## 2. Issue Mapping & Actions Taken

### 📂 Repository: `wbw-infra` (Infrastructure & IaC)
| Issue # | Action | Title | Milestone | Priority | Tags | Status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **#1** | **Updated** | `[wbw-infra] Migrate and Restructure Terraform Scripts` | Project Migration | Major | `Warlock`, `Stability` | **Closed** (Completed) |
| **#2** | **Updated** | `[wbw-infra] Update and Standardize Cloud Components, Service Accounts, WIF, and Resource Optimizations` | Project Migration | Major | `Warlock`, `Stability` | **Closed** (Completed) |
| **#3** | **Created** | `[wbw-infra] Write Repository README and Infrastructure Setup Documentation` | Project Migration | Normal | `Warlock`, `Stability` | **Closed** (Completed) |
| **#4** | **Created** | `[wbw-infra] Provision Artifact Registry Retention Policy & GitHub Actions WIF Workflow` | Project Migration | Normal | `Warlock`, `Stability` | **Closed** (Completed) |

### 📂 Repository: `wbw-architecture` (Governance & Docs)
| Issue # | Action | Title | Milestone | Priority | Tags | Status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **#1** | **Updated** | `[wbw-architecture] Write Sandbox Project Migration ADR` | Project Migration | Normal | `Warlock`, `Stability` | **Closed** (Completed) |
| **#2** | **Updated** | `[wbw-architecture] Sandbox Project Migration Blueprint` | Project Migration | Normal | `Warlock`, `Stability` | **Closed** (Completed) |
| **#3** | **Updated** | `[wbw-architecture] Migrate Cloud Migration Blueprint and Decouple Devlogs` | Project Migration | Normal | `Warlock`, `Stability` | **Closed** (Completed) |
| **#4** | **Created** | `[wbw-architecture] Write Repository README and ADR Indexing Documentation` | Project Migration | Normal | `Warlock`, `Stability` | **Closed** (Completed) |
| **#6** | **Created** | `[wbw-architecture] Add commit hook script and update bp0002 Dev Log` | Project Migration | Normal | `Warlock`, `Stability`, `documentation` | **Closed** (Completed) |

### 📂 Repository: `warlock-mcp` (FastMCP Core Application)
| Issue # | Action | Title | Milestone | Priority | Tags | Status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **#1** | **Updated** | `[warlock-mcp] Implement Domain-Driven Design (DDD) Repository Pattern` | Project Migration | Major | `Warlock`, `Stability` | **Closed** (Completed) |
| **#2** | **Updated** | `[warlock-mcp] Enforce Domain Schema Validation via Pydantic` | Project Migration | Normal | `Warlock`, `Stability` | **Closed** (Completed) |
| **#3** | **Updated** | `[warlock-mcp] Refactor Sync CLI Utility with Delta-Syncing` | Project Migration | Major | `Warlock`, `Stability` | **Closed** (Completed) |
| **#4** | **Created** | `[warlock-mcp] Package Companion Docker Container (warlock-mcp-syncer)` | Project Migration | Normal | `Warlock`, `Stability` | **Closed** (Completed) |
| **#5** | **Created** | `[warlock-mcp] Implement GitHub Actions CI/CD Workflow` | Project Migration | Major | `Warlock`, `Stability` | **Closed** (Completed) |
| **#6** | **Created** | `[warlock-mcp] Write Repository README and FastMCP Developer Documentation` | Project Migration | Normal | `Warlock`, `Stability` | **Closed** (Completed) |
| **#7** | **Updated** | `[warlock-mcp] Implement AgentSessionService Facade & Local Sister-Directory Mounting` | Project Migration | Major | `Warlock`, `Stability` | **Closed** (Completed) |

### 📂 Repository: `wbw-config` (Public Configuration)
| Issue # | Action | Title | Milestone | Priority | Tags | Status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **#1** | **Created** | `[wbw-config] Establish Public Configuration Repository and Sync Pipeline` | Project Migration | Major | `Warlock`, `Stability` | Open |
| **#2** | **Created** | `[wbw-config] Write Repository README and Public Configuration Documentation` | Project Migration | Normal | `Warlock`, `Stability` | Open |
| **#3** | **Created** | `[wbw-config] Configure Environment-Aware GitOps Workflow & Short-SHA Metadata Injection` | Project Migration | Major | `Warlock`, `Stability` | Open |

### 📂 Repository: `wbw-config-private` (Private Configuration Overlay)
| Issue # | Action | Title | Milestone | Priority | Tags | Status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **#1** | **Created** | `[wbw-config-private] Establish Private Configuration Repository and GitOps Secret Pipeline` | Project Migration | Major | `Warlock`, `Stability`, `Enhancement` | Open |
| **#2** | **Created** | `[wbw-config-private] Write Repository README and Private Secrets Pipeline Documentation` | Project Migration | Normal | `Warlock`, `Stability` | Open |
| **#3** | **Created** | `[wbw-config-private] Establish Zero-Trust GitOps Sync Workflow with Secret In-Memory Resolution` | Project Migration | Major | `Warlock`, `Stability`, `Enhancement` | Open |

### 📂 Repository: `wbw-workspace` (Meta-Repository Workspace)
| Issue # | Action | Title | Milestone | Priority | Tags | Status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **#1** | **Created** | `[wbw-workspace] Write Developer Workspace README and Bootstrap Guidelines` | Project Migration | Normal | `Warlock`, `Stability` | Open |

### 📂 Repository: `Works-by-Worrell.github.io` (GitHub Pages Organization Site)
| Issue # | Action | Title | Milestone | Priority | Tags | Status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **#3** | **Created** | `[Works-by-Worrell.github.io] Write Public Portfolio Landing Page README and Architecture Overview` | Project Migration | Normal | `Warlock`, `Stability` | Open |

### 📂 Repository: `warlock-agents` (Legacy Monorepo Sandbox)
| Issue # | Action | Title | Milestone | Priority | Tags | Status |
| :--- | :--- | :--- | :--- | :--- | :--- | :--- |
| **#48** | **Created** | `[warlock-agents] Archive and Deprecate Legacy Monorepo` | None | Minor | `Warlock`, `Stability` | Open |
| **#49** | **Created** | `[warlock-agents] Execute Monorepo Deprecation, Secret Revocation & IAM Cleanup` | None | Minor | `Warlock`, `Stability` | Open |

---

## 3. Cross-Repository Dependency Strategy

All dependencies are defined in the **Technical Implementation Plan** and **Acceptance Criteria** of the individual issues:
- **`warlock-mcp` CI/CD Workflow** (#5) lists GCP Artifact Registry, Cloud Run services, and WIF configurations (from `wbw-infra` #2) as prerequisites.
- **`wbw-config`** (#1) and **`wbw-config-private`** (#1) workflows require:
  - WIF and syncer service account configurations (from `wbw-infra` #2).
  - The built and pushed `warlock-mcp-syncer` container image (from `warlock-mcp` #5).
- **`warlock-agents`** (#49) deprecation lists successful verification of the entire migration sequence as a prerequisite.

---

## 4. Execution & Implementation Log

### Phase 1: Target GitHub Repositories Setup
- **Execution Date:** 2026-07-25
- **Key Milestones:** Setup of five target repositories (`wbw-infra`, `wbw-architecture`, `warlock-mcp`, `wbw-config`, `wbw-config-private`) under the **Works-by-Worrell** GitHub Organization.
- **Actions:**
  * Created the target repositories and synchronized governance labels, priorities, and project milestones.

### Phase 2: Platform Infrastructure Setup (`wbw-infra`)
- **Execution Date:** 2026-07-25
- **Key Milestones:** Completed `wbw-infra` IaC Foundations & Clean-Slate Rebuild.
- **Actions:**
  * **Clean-Slate Rebuild:** Executed legacy monorepo infra teardown, destroyed old state buckets, and initialized `gs://wbw-tf-state-nprd` for the non-production environment.
  * **DRY Environment Layout:** Reorganized repository structure into domain modules (`modules/warlock-mcp`) and parameterized environment strategies (`environments/nprd/` and `environments/prod/` stubs).
  * **Required Version Pinning:** Added `versions.tf` in environments to pin Terraform binary to `~> 1.9.0` and the Google provider to `~> 5.0`.
  * **Resource Re-Alignment:** Standardized global docker registry to `wbw-global-registry` with cleanup retention rules. Defined dedicated runner and GitOps syncer service accounts with least-privilege IAM roles (correcting secret accessors to `roles/secretmanager.secretAccessor`).
  * **OIDC Bootstrapping:** Configured Google OIDC workload identity provider pool and pipeline deployer service accounts via `bootstrap.tf` (**Bootstrap Pattern**).
  * **CI/CD Automation:** Set up `.github/workflows/terraform.yaml` using WIF, specifying the runner version `1.9.5` and project number `170195571079`.
  * **API Enablement:** Manually enabled `cloudresourcemanager.googleapis.com` on GCP project `worksbyworrell-nprd` and declared it as a managed service dependency in Terraform code to prevent plan-time retrieval failures.
  * **State Lock Cleanup:** Resolved orphaned GCS backend locks by running `terraform force-unlock` for lock ID `1785023455282408`.
  * **CI/CD Pipeline Verification:** Verified green CI/CD build execution running `terraform plan` successfully.
  * **DX & Git Hygiene:** Created and deployed POSIX-compliant git commit-msg validation hooks (`.githooks/commit-msg`) across all active project repositories, enforcing Conventional Commit formatting ending with issue numbers in parentheses.
  * **Repository Documentation:** Wrote detailed repository-level `README.md` and `CONTRIBUTING.md` configurations for `wbw-infra` (resolving Issue #3).

### Phase 7: Workspace Governance & Documentation
- **Execution Date:** 2026-07-25
- **Key Milestones:** Established cross-project repository documentation standards and Conventional Commit governance.
- **Actions:**
  * **Central Documentation Registry:** Created `README.md` and `CHANGELOG.md` guides for all new repositories (`wbw-workspace`, `warlock-mcp`, `wbw-config`, `wbw-config-private`, `Works-by-Worrell.github.io`).
  * **Unified Contribution Guidelines:** Created `CONTRIBUTING.md` standards in every repository and updated the central root workspace rules to establish the `gov` scope as a valid, globally accepted commit boundary.

### Phase 4: Refactor and Deploy Application Core (`warlock-mcp`)
- **Execution Date:** 2026-07-26
- **Key Milestones:** Completed DDD Repository Refactor, Pydantic Schema Validation, Agent Session Service Facade, and decoupled MCP Resources. Implemented core Ingestion Pipeline with MD5 delta-syncing.
- **Actions:**
  * **DDD Repository Pattern (Issue #1):** Designed abstract repository interfaces and implemented local filesystem and Firestore concrete strategies.
  * **Pydantic Validation (Issue #2):** Enforced runtime schema constraint validations for Agents, User Profiles, Skills, and Resources, with corresponding unit tests.
  * **Ingestion Pipeline Core (Issue #3):** Implemented a decoupled `ConfigIngestionPipeline` with MD5 checksum verification and `GITHUB_SHA`-derived version hash injection to enable delta-syncing without loading FastMCP.
  * **Service Facade & MCP Resource Decoupling (Issue #7):** Created `AgentSessionService` to compile prompt sessions and refactored resources (`agents.py`, `profiles.py`, `skills.py`, `definitions.py`) to resolve data using the service/repository layers, achieving zero direct path traversal in MCP resources.
  * **Integration Testing:** Extended the unit test suite from 40 to 58 tests, achieving 100% green coverage.
  * **YAGNI Dynamic Tools Removal & PROJECT_ROOT Elimination (Phase 4.1):** Removed unused dynamic skill tool loading (`load_dynamic_skills_tools` and `get_skill_path` from [skills.py](file:///home/raworre/Source/WBW/warlock-mcp/src/worksbyworrell/warlock/resources/skills.py)), completely eliminating any remaining `PROJECT_ROOT` path traversals from the active FastMCP resources layer.

### Phase 4 (Continued): Packaging & CI/CD Release Automation
- **Execution Date:** 2026-07-29
- **Key Milestones:** Completed Multi-stage Dockerfile packaging for both server and syncer targets, OIDC Workload Identity Federation configuration, and automated GitHub Actions deployment.
- **Actions:**
  * **Multi-Stage Containerization (Issue #4):** Designed and built a multi-stage `Dockerfile` utilizing `uv` caching layer patterns. Optimized uncompressed layer footprint (~300MB) and validated automated registry compression (~75MB). Pushed target images for `warlock-mcp` and `warlock-mcp-syncer`.
  * **OIDC WIF Access Control (Issue #2 in wbw-infra):** Updated non-production (`nprd`) and production (`prod`) bootstrap configurations in `wbw-infra` to grant workload identity federation impersonation rights (`roles/iam.workloadIdentityUser`) to the `Works-by-Worrell/warlock-mcp` repository.
  * **GHA CI/CD Release Automation (Issue #5):** Implemented `.github/workflows/deploy.yaml` triggering on pushes to `main` branch. Integrated OIDC authentication, Buildx build caching, automatic version extraction from `pyproject.toml`, short Git SHA tagging, and zero-downtime Cloud Run service deployment.
  * **Multi-Domain Private Sync (Issue #3):** Expanded the sync scope to handle private user profile overlays (`user_profile_overlays`), ensuring complete delta-syncing alignment for both public and private repository configuration domains.

### Phase 5: GitOps Config Repositories & Ingestion Automation
- **Execution Date:** 2026-07-29 to 2026-07-30
- **Key Milestones:** Completed decoupling of Markdown configurations into `wbw-config` (public) and `wbw-config-private` (secrets), fully integrated the `warlock-mcp-syncer` GitHub Actions workflow.
- **Actions:**
  * **Schema Validation Debugging:** Resolved runtime validation failures in Pydantic models by fixing missing `name` fields (adding YAML frontmatter to resources like `DEFINITION_OF_READY.md`) and refactoring `ingestion_pipeline.py` to dynamically inject IDs (`username`, `skill_id`, `resource_id`, `agent_id`) directly from filenames.
  * **GCP WIF OIDC Authentication:** Fixed GitHub Actions authorization context by configuring the `GOOGLE_APPLICATION_CREDENTIALS` environment variable to securely read the Google Auth Action's dynamically generated credentials file path, resolving workload identity permission denials.
  * **Database Targeting:** Extended the `warlock-mcp-syncer` CLI with a `--database` flag to enable non-default Firestore database specification across multiple environments.
  * **Private Overlay Optimization:** Refactored `cli.py` to decouple `--public-dir` and `--private-dir` into optional flags, preventing `argparse` execution crashes when running zero-trust private repository syncs. 
  * **Container Image Optimization:** Shipped the final multi-stage `syncer` container iteration (`v0.0.6`), landing the uncompressed deployment artifact size at ~183.6MB.
  * **Domain Routing Migration:** Rerouted Cloud Run Custom Domain Mapping via GCP Console to connect `warlock-nprd.worksbyworrell.com` directly to the new `warlock-mcp-nprd` production service.
  * **Local Dogfooding Setup (Phase 7):** Configured Antigravity CLI's native `settings.json` to mount the local `warlock-dev` MCP server pointing to absolute local `wbw-config` directories, enabling a hot-reloading development loop.
  * **Private Config Consolidation:** Synthesized 6 legacy profile/lore documents from `.private` into a single, highly-structured `raworre.md` schema-compliant User Profile. This eliminated context bloat while preserving neuro-cognitive architecture, AI alignment rules, and hardware environment data.
  * **Zero-Trust Pipeline Hardening:** Audited and corrected the `wbw-config-private` GitOps workflow YAML to explicitly utilize the `--private-dir` ingestion path, preventing catastrophic data leakage of private agents/profiles into public database collections.
  * **Dependency Injection Refactor:** Eliminated `(default)` database fallback errors by centralizing Firestore client instantiation within the DDD repository factory (`_get_firestore_client`) and enforcing explicit client dependency injection across all concrete `Firestore*Repository` classes. Removed deprecated `storage` namespace and `sync.py`. Cleaned up `Dockerfile` and GitHub Actions `deploy.yaml` by removing dead syncer pipeline stages.
  * **Enhancements Backlog:** Instantiated `ENHANCEMENTS_BACKLOG.md` within `wbw-architecture` to track future capabilities, specifically scoping private overlays for `resources` and `skills`, as well as post-migration DDD refactoring.

### Phase 6: Autonomous Job Pipeline & SSE Stabilization (Closing Phase)
- **Execution Date:** 2026-07-31
- **Key Milestones:** Fixed Cloud Run SSE connectivity and successfully orchestrated the Clutch/Torque autonomous job hunting pipeline.
- **Actions:**
  * **SSE Timeout Resolution:** Diagnosed dropped SSE connections as a Cloud Run 5-minute default timeout. Updated `wbw-infra/modules/warlock-mcp/main.tf` to force a `3600s` timeout limit.
  * **Resume Modernization:** Re-wrote the primary resume (`Worrell_Resume_2023.md`) to reflect exact "Meat & Salt" scaling metrics, legacy monolith navigation (J.B. Hunt), and AI workflow engineering. 
  * **Exfiltration Protocol Injection:** Updated the `raworre` private profile with a strict $150k target, remote/async validation rules, and a lateral fallback safety net.
  * **Orchestrator Pipeline:** Built `nightly_evaluator.py`, a zero-friction ingestion script that scrapes job descriptions from `hopper.txt`, invokes `gemini-3.6-flash` against the Clutch JSON schema, and successfully routes "PROCEED" verdicts through the MCP to generate YouTrack issues via Torque.
