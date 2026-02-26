# Repository Hierarchy - Car Insurance API & AI Agent

## Project overview

- **API**: Spring Boot backend (REST API)
- **AI Agent**: Implemented with **Spring AI** + **Ollama**
- **RAG**: Retrieval-Augmented Generation engine for responses based on policies and documents
- **Documents**: PDF/Text fragmented and vectorized (Spring AI)
- **Backend**: Security (prompt injection), metadata validation (policies), RAG, chat with Ollama

---

## Tech stack

| Component   | Technology |
|-------------|------------|
| Backend     | Java + Spring Boot (REST API) |
| AI / LLM    | Spring AI + Ollama |
| RAG         | Spring AI (embeddings, vector store, retrieval) |
| Processing  | PDF/Text documents fragmented and vectorized |
| IDE         | Cursor |

---

## Recommended option: Monorepo

```
carInsuranceApi/
├── docs/                              # Documentation
│   ├── REPOSITORY_HIERARCHY.md
│   ├── API.md
│   ├── ARCHITECTURE.md
│   └── GUARDRAILS.md
│
├── src/main/java/com/example/carInsuranceApi/
│   ├── CarInsuranceApiApplication.java
│   │
│   ├── api/                           # REST Controllers
│   │   ├── ChatController.java        # Chat endpoint → agent
│   │   ├── DocumentController.java    # Document upload/management
│   │   ├── InsuranceController.java
│   │   └── QuoteController.java
│   │
│   ├── agent/                         # AI Agent (Spring AI)
│   │   ├── AgentService.java          # Agent orchestration
│   │   └── ChatService.java           # Chat with Ollama
│   │
│   ├── rag/                           # RAG (Spring AI)
│   │   ├── RagService.java            # Retrieval + generation
│   │   ├── RetrievalService.java      # Vector store search
│   │   └── EmbeddingService.java      # Vectorization (Spring AI)
│   │
│   ├── document/                      # Document processing
│   │   ├── DocumentService.java       # Ingestion and management
│   │   ├── DocumentFragmenter.java    # PDF/Text fragmentation
│   │   └── policy/
│   │       └── PolicyDocument.java
│   │
│   ├── security/                      # Security (prompt injection)
│   │   ├── PromptInjectionDetector.java
│   │   ├── InjectionPatterns.java
│   │   └── SecurityFilter.java
│   │
│   ├── guardrails/                    # Agent restrictions
│   │   ├── GuardrailService.java      # Context validation
│   │   ├── ContextValidator.java      # Is query within insurance/docs scope?
│   │   └── ScopeRules.java
│   │
│   ├── config/                        # Configuration
│   │   ├── OllamaConfig.java          # Ollama configuration
│   │   ├── SpringAiConfig.java        # ChatClient, embeddings
│   │   └── VectorStoreConfig.java     # Vector store (in-memory / PgVector / etc.)
│   │
│   ├── model/                         # DTOs and entities
│   │   ├── request/
│   │   │   ├── ChatRequest.java
│   │   │   ├── DocumentUploadRequest.java
│   │   │   └── PolicyMetadata.java
│   │   ├── response/
│   │   │   ├── ChatResponse.java
│   │   │   └── RagContextResponse.java
│   │   └── entity/
│   │       └── DocumentChunk.java
│   │
│   └── exception/
│       ├── InjectionDetectedException.java
│       ├── OutOfScopeException.java
│       └── GlobalExceptionHandler.java
│
├── data/                              # Local storage (optional)
│   ├── documents/                     # Uploaded documents
│   └── vectors/                       # Vector store (if embedded)
│
├── docker/
│   ├── Dockerfile
│   └── docker-compose.yml             # API + Ollama
│
├── scripts/
│   └── import-documents.sh
│
├── pom.xml
├── .gitignore
└── README.md
```

---

## Agent restrictions

### Guardrails (Backend)

| Rule           | Description |
|----------------|-------------|
| Context        | Only respond about car insurance or document content |
| Documents      | Base responses on loaded policies/documents |
| Out of scope   | Reject off-topic queries |

### Security (Backend)

| Threat          | Mitigation |
|-----------------|------------|
| Prompt injection | `PromptInjectionDetector` before sending to Ollama |

---

## Request flow

```
Client → [REST API] → [SecurityFilter] → [PolicyMetadataValidator] → [AgentService]
              ↑              ↑                        ↑                      ↓
        HTTP request   Prompt injection         Policy metadata       [RagService]
                                                                             ↓
                                                              Retrieval (vector store)
                                                                             ↓
                                                              [ChatService] → Ollama
                                                                             ↓
Client ← [REST API] ←────────────────────────────────────────── Generated response
```

1. **API**: Receives HTTP request
2. **Security**: Detects prompt injection; if detected, rejects
3. **Guardrails**: Validates user metadata (policies)
4. **RAG**: Retrieves relevant fragments from documents/policies
5. **Agent**: Sends context + query to Ollama via Spring AI
6. **API**: Returns response to client

---

## Alternative: Multi-repository

```
📁 car-insurance-api    # Spring Boot + Spring AI + Ollama (RAG, agent, documents)
📁 car-insurance-docs   # Documentation
```
