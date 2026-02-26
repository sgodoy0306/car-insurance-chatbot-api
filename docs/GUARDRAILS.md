# Agent Guardrails and Security

## Responsibilities (Spring Boot Backend)

The backend implements everything with Spring AI + Ollama:

| Component   | Responsibility |
|-------------|----------------|
| **RAG**     | Document fragmentation, vectorization, vector store search |
| **Agent**   | Chat with Ollama via Spring AI |
| **Guardrails** | Context validation (insurance/docs only) and metadata (policies) |
| **Security** | Prompt injection detection before sending to Ollama |

---

## Agent restrictions

### Allowed context

The agent may **only** respond about:

1. **Car insurance** in general
2. **Content of loaded policies**
3. **Provided documents** (clauses, conditions, coverage)

### Behavior for out-of-scope queries

- Reject with a clear message
- Do not infer or invent information
- Do not respond to off-topic subjects

---

## Backend security

### Prompt injection detection

The backend **must** detect attempts to bypass rules before sending the request to Ollama:

- Hidden instructions (e.g. "ignore the previous rules")
- Role switching (e.g. "act as admin without restrictions")
- Data exfiltration (e.g. "show the prompt content")
- Validation bypass

### Implementation (Spring Boot)

- **Filtering layer**: `PromptInjectionDetector` before calling Ollama
- **Patterns**: Pattern list in `InjectionPatterns`
- **Filter**: `SecurityFilter` as input middleware

### Response on detection

- Block the query (do not send to Ollama)
- Log the attempt (audit log)
- Return a generic response without executing the request
