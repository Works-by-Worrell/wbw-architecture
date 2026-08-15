# ADR 0000: Tailscale Remote Execution Baseline (Deprecated)

## Status
*   **Status:** Deprecated (Superseded by ADR 0001)
*   **Author:** Roger Worrell
*   **Date:** 2026-06-15 (Reconstructed)

## 1. Context & Problem Statement
The initial Works-by-Worrell open-source contribution agents were developed inside a monolithic repository (`~/Source/WBW`). The architecture requires running resource-intensive local LLMs and executing scraping workloads. Development occurs asynchronously across two distinct hardware profiles: a high-powered Windows Gaming PC (desktop) and a mobile MSI laptop. We need a way to execute these workloads seamlessly across both devices without duplicating environments or paying for cloud compute.

## 2. Decision Driver & Trade-offs
*   **Budget:** Hard constraint of $0.00 infrastructure cost.
*   **Mobility:** Must be able to execute agents from the laptop while away from the desktop.
*   **Complexity:** Local networking (NAT traversal) is notoriously difficult.

## 3. Resolution
Implement **Tailscale** to create a secure, flat mesh network between the MSI laptop and the Gaming PC. The Gaming PC will act as the primary execution node. The MSI laptop will trigger remote execution via SSH over the Tailscale VPN.

## 4. Consequences
*   **Positive:** Maintained the $0.00 budget constraint by utilizing existing hardware.
*   **Positive:** Successfully bridged the two devices without opening public firewall ports.
*   **Negative (Fatal):** **High Environmental Friction.** Synchronizing the Python virtual environments, system paths, and hardware-specific configurations across the Tailscale bridge proved unmanageable. If the desktop was asleep, execution failed. The overhead of managing the local networking mesh overshadowed the actual development of the agentic systems. This friction directly violates Principle 1 (Frictionless Onboarding) and necessitates a migration to a highly-available environment.
