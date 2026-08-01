# Warlock Project: Enhancements Backlog

This document serves as a staging ground for architectural enhancements, technical debt cleanup, and feature requests that are discovered during active development but fall outside the scope of current milestones. Items here should eventually be triaged and converted into formal GitHub Issues.

---

## 1. GitOps & Configuration Pipeline
### **Support Private Overlays for Resources and Skills**
- **Repository:** `warlock-mcp` (and `wbw-config-private`)
- **Context:** Currently, the `warlock-mcp-syncer` CLI only looks for `agents` and `profiles` when scanning the `--private-dir`. 
- **Requirement:** Update `cli.py` to support `private_resources_dir` and `private_skills_dir`. This will allow the ingestion of proprietary corporate architecture documents, private API keys, and sensitive local scripts without exposing them in the public `wbw-config` repository.
- **Impact:** Enables full zero-trust, closed-source capabilities for specific agent tools and context domains.

## 2. Technical Debt & Refactoring
### **Refactor Legacy Private Agents and Profiles**
- **Repository:** `wbw-config-private`
- **Context:** During the Phase 5 repository split, legacy profiles (e.g., from `.private/profiles`) and agents were "lifted and shifted" directly into the new private GitOps repo to validate the pipeline. 
- **Requirement:** Audit and rewrite the lifted markdown files to properly align with the new layered architecture. Strip out redundant system prompt data, rely on the core Agent Session Service Facade for resource resolution, and ensure the content conforms strictly to the Pydantic schema constraints.
- **Impact:** Cleaner prompt contexts, reduced token bloat, and stricter separation of concerns between agents, profiles, and resources.

## 3. Infrastructure & Deployment
*(No pending infrastructure enhancements at this time)*

---
*Note: Once an item is converted into a GitHub Issue, move it to a "Triaged" section or remove it from this backlog.*
