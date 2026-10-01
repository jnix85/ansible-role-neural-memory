# ADR-0004: Dedicated Backup Sidecar Container (postgres-backup-local)

- **Status:** Accepted
- **Date:** 2026-09-15
- **Deciders:** Jason Parks, AI Pair

---

## Context and Problem Statement

Neural Memory databases store critical long-term associative knowledge graphs, reflexes, and preferences. Automated point-in-time database snapshots must execute reliably and independently of host systemd cron setups.

---

## Decision Outcome

We decided to deploy the `prodrigestivill/postgres-backup-local:16` sidecar container within the stack:

1. **Scheduled Automated Dumps**:
   - Runs a cron schedule (`SCHEDULE: "0 2 * * *"`) inside the container.
   - Takes gzip-compressed `pg_dump` archives into `/var/backups/neural-memory/`.
   - Manages automatic rolling retention via `BACKUP_KEEP_DAYS: 14`.

2. **Dual-Layer On-Demand Backups**:
   - Deploys `/usr/local/sbin/neural-memory-backup.sh` CLI wrapper on the host that invokes `/backup.sh` inside the running backup container.
   - `playbooks/backup.yml` triggers this backup and reports status and generated file sizes.

---

## Consequences

### Positive
- **Fully Encapsulated**: Backup runtime and retention logic live inside the container stack.
- **Engine Agnostic**: Works identically under Podman Quadlets and Docker Compose.

### Negative / Trade-offs
- **Sidecar Memory Overhead**: Lightweight backup container runs continuously (~20MB RAM).
