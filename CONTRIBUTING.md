# Contributing to Works-by-Worrell Architecture

This document outlines the branch taxonomy, commit message standards, and workflows specifically for the **wbw-architecture** repository.

---

## 1. Branch Strategy & Taxonomy

All development work MUST occur on a feature or task branch before targeting the `main` branch. All branches MUST align with one of the following prefix categories:

### Branch Prefix Categories

*   `docs/` - Runbook updates, architectural design documents, ADRs, blueprints, and devlogs
*   `fix/` - Corrections to technical specifications, diagrams, or typos
*   `chore/` - Maintenance, configuration updates, or directory restructuring

### Branch Naming Convention

All branches MUST follow this format: 
`<type>/issue-<id>-<description>` or `<type>/phase<num>-<short-description>`

**Examples:**
*   `docs/phase2-repo-split`
*   `fix/issue-4-typo-blueprint`
*   `docs/issue-41-adr-template`

---

## 2. Commit Message Conventions

We strictly adhere to the [Conventional Commits](https://www.conventionalcommits.org/) specification. This enables automated release notes, changelog generation, and clear system auditability.

### Commit Format

Commit messages MUST follow the structure:
```
docs(<scope>): <short description>

[Optional body explaining design rationale or context]

[Optional footer(s) for issue linking, e.g., Closes #12]
```

*Note: Since this is a pure documentation repository, the commit type is typically `docs` (or `chore`/`fix` for tooling/typos).*

### Scope Boundaries (Repository Specific)

When writing a commit, the `scope` MUST represent the logical area of this architecture repository:

| Scope | Logical Boundary | Example Commit |
| :--- | :--- | :--- |
| `adr` | Architecture Decision Records (changes inside `adrs/`) | `docs(adr): approve repository split ADR 0002` |
| `blueprint` | System blueprints and implementation guides (inside `blueprints/`) | `docs(blueprint): specify testing criteria for sync tool` |
| `devlog` | Historical logs and execution timelines (inside `devlogs/`) | `docs(devlog): log phase 1 execution milestone details` |
| `gov` | Governance docs, templates, and SOPs (e.g., README, CONTRIBUTING) | `docs(gov): update architectural submission template` |

---

## 3. Pull Request & Submission Guidelines

Before submitting a Pull Request:

1.  **Alignment with README SOP:** Every new ADR or Blueprint MUST conform to the governance workflows and templates defined in the [README.md](README.md).
2.  **Linting & Verification:** Verify that any Mermaid diagrams render correctly without syntax errors.
3.  **Squash & Merge:** Squash intermediate commits before merging. Every commit on `main` MUST represent a clean, approved version of the architectural record.
