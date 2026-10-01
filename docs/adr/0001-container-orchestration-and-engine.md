# ADR-0001: Container Orchestration and Dual Engine Support (Podman Quadlets & Docker Compose)

- **Status:** Accepted
- **Date:** 2026-09-15
- **Deciders:** Jason Parks, AI Pair

---

## Context and Problem Statement

Neural Memory consists of several interconnected container services: PostgreSQL with `pgvector`, FastAPI REST & Dashboard, Streamable HTTP MCP server, Nginx Ingress, and Backup Sidecar.

Infrastructure standards on Debian/Ubuntu benefit from rootless, daemonless container execution using systemd native Podman Quadlets, while certain existing environments still rely on Docker Compose.

We need a container orchestration strategy that supports rootless execution via Podman Quadlets while preserving full compatibility for Docker Compose deployments.

---

## Decision Outcome

We decided to support a dual-engine architecture controlled via `neural_memory_container_engine` (`podman` | `docker`, default: `podman`):

1. **Podman Quadlets (`neural_memory_container_engine: podman`)**:
   - Deployed under a dedicated unprivileged system user (`neuralmemory` / UID 10001) with `loginctl enable-linger`.
   - Quadlet files (`.container`, `.volume`, `.network`) are placed in `~neuralmemory/.config/containers/systemd/`.
   - Uses an isolated Podman user bridge network (`neural-memory.network`) enabling internal container DNS resolution (`postgres:5432`, `mcp:8765`, `web:8000`).
   - Supervised directly by user-session systemd (`systemctl --user status neural-memory-web.service`).

2. **Docker Compose (`neural_memory_container_engine: docker`)**:
   - Generates `/opt/neural-memory/docker-compose.yml` and `/etc/systemd/system/neural-memory.service`.
   - Manages the complete stack as an atomic systemd service.

---

## Consequences

### Positive
- **Rootless Security**: Eliminates root container daemon attack surfaces by running within unprivileged user namespaces.
- **Native Systemd Integration**: Podman Quadlets integrate seamlessly into host cgroups, slice management, and journald logging.
- **Backward Compatibility**: Docker Compose remains fully supported for legacy hosts.

### Negative / Trade-offs
- **Ansible Dual Paths**: Role maintains two execution and template paths (`tasks/podman.yml` and `tasks/compose.yml`).
