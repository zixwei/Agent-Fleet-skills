---
name: ai-llm-agent-security
description: Assess authorized LLM applications, RAG systems, AI agents and tool integrations for prompt injection, data leakage, excessive agency, insecure retrieval, model supply-chain and unsafe tool-permission weaknesses using controlled test fixtures.
---

# AI, LLM and agent security assessment

## Purpose

Review generative-AI applications and agentic systems, including chat assistants, RAG pipelines, vector databases, tool-using agents, model gateways and MCP integrations. Evaluate trust boundaries between user input, retrieved content, models, memory, tools, plugins and external services.

This skill focuses on architecture, configuration, non-destructive evaluation and safety controls. It does not seek to extract real secrets, bypass production safeguards or cause agents to execute unauthorized actions.

## Prerequisites

- Written authorization identifies the application, environment, model/provider, APIs, datasets, vector stores, tools/MCP servers, test accounts and permitted test cases.
- [Engagement scoping](../engagement-scoping/SKILL.md) is complete.
- Use a staging environment, synthetic documents and test identities. Disable access to real customer data and production tools.
- The owner provides an approved test corpus and a fixed model/deployment configuration. Record model version, system prompt revision, retrieval index and tool schema hashes.
- Define usage budgets, rate limits, model-provider data retention rules and emergency stop conditions.

## Standards and tools

Use the current OWASP GenAI Top 10 for LLM Applications (2026) and the OWASP MCP Top 10 project; record whether the MCP guidance is a draft/beta and pin its exact revision. Also consult the OWASP Agentic AI and Agent Control resources, NIST AI RMF and MITRE ATLAS.

- **Promptfoo:** repeatable prompt/model security evaluations using a local or staging test harness. [Docs](https://www.promptfoo.dev/docs/)
- **Garak:** model and application vulnerability scanner; use only against owned test endpoints and synthetic corpora. [Project](https://github.com/NVIDIA/garak)
- **PyRIT:** structured red-team evaluation framework for controlled environments. [Project](https://github.com/Azure/PyRIT)
- **Giskard:** LLM/RAG evaluation and testing. [Docs](https://docs.giskard.ai/)
- **OWASP MCP Security guidance:** review protocol endpoints, capability grants and tool boundaries. [Project](https://owasp.org/www-project-mcp-top-10/)

## Workflow

### 1. Map the system and trust boundaries

Inventory UI/API entry points, system/developer prompts, model/provider, retrieval sources, vector stores, memory, plugin/tool interfaces, MCP servers, identity context, logs, moderation and downstream actions. Trace which components can read data or cause side effects.

### 2. Review identity and authorization propagation

Verify that user identity, tenant, role and resource permissions are carried across retrieval and every tool call. Ensure the model cannot elevate privileges by changing a prompt, tool argument, tenant identifier or claimed role. Test with synthetic users and records.

### 3. Evaluate prompt injection and untrusted context handling

Use benign canary instructions embedded in synthetic documents and user prompts. Verify that retrieved or quoted content is treated as data, not trusted policy. Check whether the application distinguishes user, developer, system and retrieved-content trust levels. Do not target public models or real user sessions without separate permission.

### 4. Review sensitive-data boundaries

Check whether prompts, retrieval results, embeddings, caches, traces, model logs or error messages can expose synthetic secrets across users or tenants. Verify data minimization, redaction, retention, deletion and provider data-use controls. Never use real customer secrets as canaries.

### 5. Review RAG and retrieval integrity

Inspect ingestion provenance, source authorization, document ACL preservation, chunk metadata, tenant filters, embedding model/version, vector database access, deletion propagation and retrieval ranking. Test cross-tenant isolation using synthetic corpora. Review indirect prompt injection from indexed documents.

### 6. Review tool/agent permissions

For every tool, MCP server or plugin record its purpose, allowed methods, input schema, identity context, read/write capabilities, network destinations, secrets access and approval policy. Apply least privilege, explicit user confirmation for consequential actions, argument validation, output filtering and per-tool timeouts. The model must not grant itself new tools or broaden scope.

### 7. Review MCP-specific controls

Assess server identity and origin, transport security, authentication, authorization, session lifecycle, capability negotiation, tool/resource/prompt schemas, resource URI validation, content trust, error handling, audit logging and dependency provenance. Review tool poisoning and confused-deputy risks using a test server. Never connect unreviewed MCP servers to real accounts or production credentials.

### 8. Review output handling and downstream effects

Review HTML/Markdown rendering, code execution interfaces, SQL/query generation, browser automation, email/file operations and API actions. Ensure model output is not treated as trusted executable code. Use mocked downstream systems and harmless markers.

### 9. Evaluate model and dependency supply chain

Record model/provider and version, model artifact provenance if self-hosted, adapter/finetune lineage, tokenizer, prompt template, embeddings, vector database, agent framework and plugin dependencies. Check license, signing, integrity and update controls.

### 10. Run a bounded evaluation suite

Create a versioned suite with positive/negative cases, expected policy outcome, synthetic test data, model parameters, repeat count, budget and severity rubric. Track false positives and nondeterminism. Do not claim a single model response proves a security property.

### 11. Report and retest

Record exact application/model/prompt/index/tool versions, scenario ID, synthetic identity, expected vs observed behavior, reproducibility, impact boundary, mitigation and residual risk. Retest the same scenario after fixes.

## Output

System/data-flow diagram, trust-boundary map, identity propagation review, prompt-injection and retrieval test results, tool/MCP permission matrix, privacy and logging findings, model supply-chain inventory, evaluation dataset and remediation plan.

## Safety constraints

- Use isolated staging and synthetic data. No real customer prompts, secrets, documents or accounts.
- Do not attempt extraction of real system prompts, tokens, private documents or provider credentials.
- Do not cause agents to send emails, deploy code, delete files, modify production records or invoke unapproved tools.
- No persistent agents, lateral movement, uncontrolled resource consumption or evasion.
- Stop immediately if a tool accesses a production system, a cross-tenant record appears or the model performs an unintended side effect.

## References

- [OWASP GenAI Security Project](https://genai.owasp.org/)
- [OWASP GenAI LLM Top 10 2026](https://genai.owasp.org/resource/owasp-genai-llm-top-10-2026/)
- [OWASP MCP Top 10](https://owasp.org/www-project-mcp-top-10/)
- [NIST AI Risk Management Framework](https://www.nist.gov/itl/ai-risk-management-framework)
- [MITRE ATLAS](https://atlas.mitre.org/)
- [Promptfoo](https://www.promptfoo.dev/docs/)
- [Microsoft PyRIT](https://github.com/Azure/PyRIT)
