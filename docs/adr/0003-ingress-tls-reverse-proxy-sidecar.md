# ADR-0003: Bundled Nginx Reverse Proxy Sidecar for Local TLS and Routing

- **Status:** Accepted
- **Date:** 2026-09-15
- **Deciders:** Jason Parks, AI Pair

---

## Context and Problem Statement

Neural Memory exposes two primary HTTP endpoints:
1. Streamable HTTP MCP JSON-RPC server (`:8765/mcp`)
2. FastAPI REST API and Web Dashboard (`:8000/ui`)

Distributed agents connect over local or remote networks. Unencrypted HTTP exposes Bearer tokens (`Bearer nmk_...`) in transit. Furthermore, MCP streaming over JSON-RPC requires persistent streaming connections without response buffering interruptions.

---

## Decision Outcome

We decided to bundle a lightweight **Nginx** reverse proxy sidecar (`nginx:alpine`) inside the stack:

1. **Integrated Ingress Routing**:
   - Routes HTTPS traffic on port 443 / 8443 to the internal MCP service (`/mcp` -> `mcp:8765/mcp`) and Web dashboard (`/` -> `web:8000`).
   - Configured with `proxy_buffering off;` and `proxy_read_timeout 3600s;` to ensure continuous SSE and chunked JSON-RPC streams without truncation or timeout drops.

2. **TLS Certificate Lifecycle**:
   - Supports custom certificates via Ansible Vault variables (`neural_memory_tls_cert` and `neural_memory_tls_key`).
   - Automatically generates a local self-signed TLS certificate if custom certificates are not supplied, ensuring out-of-the-box encrypted transport.

---

## Consequences

### Positive
- **Guaranteed In-Transit Encryption**: All API keys and memory payloads are encrypted across LAN/WAN links.
- **Optimized for Streaming**: Nginx configuration includes explicit HTTP/1.1 chunked/SSE buffer bypass for MCP RPC.

### Negative / Trade-offs
- **Self-Signed Trust**: Initial self-signed certificates require clients to either trust the local cert or skip strict verification unless replaced with trusted certificates.
