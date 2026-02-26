# Car Insurance API & AI Agent

Car insurance API with an **AI Agent** implemented using **Spring AI** + **Ollama**. RAG engine for responses based on loaded policies and documents.

## Tech stack

| Component   | Technology |
|-------------|------------|
| Backend     | Java + Spring Boot (REST API) |
| AI / LLM    | Spring AI + Ollama |
| RAG         | Spring AI (embeddings, vector store, retrieval) |
| Processing  | PDF/Text documents fragmented and vectorized |
| IDE         | Cursor |

## Project structure

```
carInsuranceApi/
├── docs/                    # Documentation
├── src/main/java/.../
│   ├── api/                 # REST Controllers
│   ├── agent/               # AI Agent (Spring AI + Ollama)
│   ├── rag/                 # RAG: retrieval, embeddings, vector store
│   ├── document/            # Document fragmentation and vectorization
│   ├── security/            # Prompt injection detection
│   ├── guardrails/          # Context and metadata validation
│   ├── config/              # Ollama, Spring AI, vector store configuration
│   ├── model/               # DTOs and entities
│   └── exception/           # Error handling
├── data/                    # Documents and vector store (local)
├── docker/                  # API + Ollama
└── scripts/
```

## Agent restrictions

- **Guardrails**: Only respond about car insurance or document content
- **Security**: Prompt injection detection before sending to Ollama

## Documentation

- [Repository hierarchy](docs/REPOSITORY_HIERARCHY.md)
- [Guardrails and security](docs/GUARDRAILS.md)
