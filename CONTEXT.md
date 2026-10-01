# Domain Context & Ubiquitous Language: Neural Memory Infrastructure

This document establishes the ubiquitous language and domain model for the Neural Memory infrastructure automation role. It serves as the single source of truth for terminology across AI agents, human operators, and automation playbooks.

---

## 1. Core Domain Glossary

### Agent
An autonomous or interactive AI workflow (e.g., Claude Code, Antigravity, OpenClaw, Goose) that queries or writes state to long-term memory.

### Brain (Memory Bank)
A logical, isolated cognitive workspace containing an agent or cluster of agents' memories, facts, reflexes, and context graphs.

### Memory Chunk
An atomic unit of persistent knowledge stored within a Brain, categorized by type (experience, fact, rule, decision) and indexed with high-dimensional vector representations.

### Spreading Activation
A cognitive recall mechanism that traverses associative memory graphs, scoring interconnected nodes by relevance and decay distance rather than simple vector proximity alone.

### Memory Consolidation
A periodic background optimization process that clusters related memories, deduplicates overlapping records, and synthesizes higher-level insights.

### Cognitive Decay
The graceful temporal degradation of memory salience over time for unreinforced or ephemeral knowledge.

### Reflex Distillation
The automated synthesis of repeated patterns and corrections into instantaneous, high-priority heuristics (reflexes) that trigger prior to general recall.

### MCP (Model Context Protocol) Endpoint
A standardized protocol interface exposing memory tools, resources, and prompts to connected agent clients over network transports.

### Streamable HTTP Transport
A persistent, bidirectional HTTP/JSON-RPC transport enabling multiple concurrent agents on distributed hosts to share a single MCP service without spawning local subprocesses.

### Vector Embedding
A dense mathematical representation (e.g., 1536 dimensions) representing the semantic meaning of text chunks, queried via nearest-neighbor vector similarity.

### Multi-Host Brain Architecture
A deployment topology where agents across disparate physical and virtual machines (e.g. Mac workstations, Linux worker nodes) connect to a single authoritative Neural Memory instance over local or overlay networks.

### Container Engine Backend
The host runtime environment (e.g., Podman rootless Quadlets or Docker Compose daemon) responsible for isolating and supervising the database, application, and proxy workloads.

### Ingress & TLS Termination
The entry point component (e.g., bundled reverse proxy or edge gateway) responsible for routing traffic, securing transports with TLS, and enforcing access control.

### Backup Snapshot
A point-in-time compressed dump of the underlying relational and vector database, archived according to configured retention schedules.

### Storage Backend
The relational and vector persistence layer (PostgreSQL with `pgvector` extension) storing structured tables and vector indexes.

### Brain Migration
The atomic point-in-time transfer of Brain state (PostgreSQL pgvector knowledge graph and `/data` volume) from host-based runtimes to Kubernetes via verified dump bundles prior to traffic cutover.

### Cold Standby
An offline host instance whose container services are halted and auto-restart disabled, but whose local database and filesystem volumes are preserved in-place as a frozen recovery fallback.

### Split-Brain Divergence
The failure state occurring when multiple uncoordinated AI coding assistants concurrently write memories to disconnected Brain instances, causing unmergeable graph divergence.

### Canary Smoke Test
A brief (10-minute) targeted verification session on a single client testing MCP stream stability and memory read/write latency on a newly migrated Brain before global client cutover.
