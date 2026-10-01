# ADR-0005: Centralized LLM Gateway Routing with Direct Provider Fallbacks

- **Status:** Accepted
- **Date:** 2026-09-15
- **Deciders:** Jason Parks, AI Pair

---

## Context and Problem Statement

Neural Memory requires access to Large Language Models (LLMs) and vector embedding models for:
- 1536-dimension text embedding generation for memory chunks.
- Cognitive consolidation, pattern synthesis, and reflex distillation.

In self-hosted environments, requests are typically routed through a centralized LLM Gateway (such as [Bifrost](https://github.com/maximumpower/bifrost) or LiteLLM) for caching, rate-limiting, and cost tracking. However, gateway downtimes or maintenance should not completely disable memory operations if upstream direct API keys (OpenAI, Gemini, Anthropic) are provisioned.

---

## Decision Outcome

We decided on a centralized gateway configuration pattern with explicit provider fallbacks:

1. **Primary Routing via LLM Gateway**:
   - Primary `OPENAI_BASE_URL` and `OPENAI_API_KEY` are pointed to the gateway URL (e.g., `neural_memory_llm_gateway_url`).
   - Default chat and embedding models route through the gateway.

2. **Direct Provider Fallback Support**:
   - Secondary provider API keys (`neural_memory_gemini_api_key`, `neural_memory_anthropic_api_key`, `neural_memory_openai_api_key`) are configured in the environment file.
   - If the gateway is unavailable or unconfigured, the application falls back seamlessly to direct provider endpoints.

---

## Consequences

### Positive
- **High Availability**: Memory services retain operational resilience during gateway maintenance.
- **Unified Observability**: Centralized gateway retains logging and metrics for all standard operations.

### Negative / Trade-offs
- **Credential Management**: Operator may need to manage both gateway credentials and individual upstream provider API keys in Ansible Vault.
