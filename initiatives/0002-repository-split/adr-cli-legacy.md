# ADR 002: Migrate CLI Tooling to Dedicated Python Package (wbw-cli)

## Status
Draft

## Context
Currently, the Works-by-Worrell agentic workflow relies on local Bash scripts (`agy-start.sh`, `warlock-auth-cycler.sh`, and `mcp-bootstrap.py`) to authenticate the operator, dynamically resolve agent overlays from the `warlock-mcp` server, and boot the Antigravity CLI. These scripts are untracked and unversioned locally, which introduces friction for portability, distribution, and environment consistency across the organization (even if the organization is currently N=1).

## Decision
We will migrate the CLI startup logic into a dedicated Python package repository (`wbw-cli`). The package will:
1. Define a standard console script entrypoint (e.g., `wbw-daemon`).
2. Manage its own dependencies (e.g., the `mcp` client SDK).
3. Handle token refreshing and Warlock MCP bootstrapping natively in Python.
4. Launch the underlying `agy` process natively as a subprocess.

## Distribution Method
The package will be distributed and installed via `pipx`:
```bash
pipx install git+https://github.com/Works-by-Worrell/wbw-cli.git
```

## Consequences
### Positive
* **Paved Road DevEx:** Engineers can install the entire toolchain with a single `pipx` command.
* **Dependency Isolation:** `pipx` isolates the CLI dependencies from the user's system Python, preventing conflict.
* **Versioning:** The wrapper logic becomes centrally versioned and controlled.

### Negative
* Requires maintaining an additional lightweight GitHub repository.
* Requires engineers to have `pipx` installed on their host machine.
