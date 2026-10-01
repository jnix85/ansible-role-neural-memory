# ADR-0002: Dual Database Topology (Local Containerized pgvector & External PostgreSQL)

- **Status:** Accepted
- **Date:** 2026-09-15
- **Deciders:** Jason Parks, AI Pair

---

## Context and Problem Statement

Neural Memory requires a PostgreSQL database with the `pgvector` extension enabled for high-dimensional vector similarity searches and structured entity storage.

In standalone single-node deployments (e.g. Proxmox LXC container dedicated to Neural Memory), managing a local containerized PostgreSQL instance is convenient and isolated. However, in enterprise or multi-service home lab environments, an existing managed or clustered PostgreSQL instance (e.g., deployed via `ansible-role-postgresql` or HA Patron/PgBouncer) may already exist and be preferred.

---

## Decision Outcome

We decided to support a dual database deployment topology controlled via `neural_memory_postgres_external` (boolean, default: `false`):

1. **Local Containerized Mode (`neural_memory_postgres_external: false`)**:
   - Provisions a `pgvector/pgvector:pg16-trixie` container within the local container stack/pod.
   - Automatically executes the database initialization script (`postgres-init.sh`) to create the database, user, and `CREATE EXTENSION IF NOT EXISTS vector;`.
   - Mounts persistent volume at `neural_memory_data_dir/postgres`.

2. **External Database Mode (`neural_memory_postgres_external: true`)**:
   - Skips local database container provisioning and port binding.
   - Connects the Web and MCP services directly to `neural_memory_postgres_host` and `neural_memory_postgres_port` using provided credentials.
   - Requires the target external PostgreSQL instance to have `pgvector` installed and accessible.

---

## Consequences

### Positive
- **Architectural Scalability**: Allows Neural Memory to fit into both lightweight single-node VMs/LXCs and complex multi-server environments with central databases.
- **Resource Efficiency**: Avoids running redundant PostgreSQL daemons when a dedicated database server is already present.

### Negative / Trade-offs
- **Prerequisite Validation**: When using external databases, the operator is responsible for ensuring `pgvector` extension compatibility on the external server.
