# Project Context: ansible-role-neural-memory

**Type:** Ansible Role / Infrastructure Automation  
**Target:** Debian 12 (Bookworm), Debian 13 (Trixie), Ubuntu 22.04/24.04  
**Service:** Neural Memory (Persistent memory for AI agents) — Docker Compose stack with PostgreSQL + pgvector, FastAPI Web/Dashboard, Streamable HTTP MCP Server, and automated backup timers.

---

## Layout

Standard standalone Ansible project structure:
- `ansible.cfg`
- `inventory/`
  - `hosts.yml` (Pre-configured for Proxmox LXC `neural-memory1` and remote host `sigrun`)
  - `group_vars/`
- `playbooks/`
  - `site.yml` (Complete deployment: docker, postgres, compose stack, systemd unit, backup timer)
  - `deploy.yml` (Application deployment / update only)
  - `backup.yml` (On-demand PostgreSQL database backup)
- `roles/neural_memory/` (Core role tasks, handlers, templates, and defaults)

---

## Common Commands

```bash
# Syntax check
ansible-playbook -i inventory/hosts.yml playbooks/site.yml --syntax-check

# Dry-run / Check mode
ansible-playbook -i inventory/hosts.yml playbooks/site.yml --check --diff

# Full deployment
ansible-playbook -i inventory/hosts.yml playbooks/site.yml

# Fast application update (skip package installation)
ansible-playbook -i inventory/hosts.yml playbooks/deploy.yml

# Trigger manual backup
ansible-playbook -i inventory/hosts.yml playbooks/backup.yml
```

---

## Multi-Host Brain Architecture

Neural Memory acts as the unified multi-host memory bank across Mac and Linux agent sessions:
- MCP Streamable HTTP endpoint: `http://<host>:8765/mcp`
- Web Dashboard & REST API: `http://<host>:8000/ui`
- PostgreSQL vector backend: `pgvector/pgvector:pg16-trixie`
- LLM Gateway: Compatible with Bifrost or OpenAI-compatible gateways.
