# Jerarquía de Repositorios - Car Insurance API & Agente IA

## Visión general del proyecto

- **API**: Backend Spring Boot (REST API)
- **Agente IA**: Implementado con **Spring AI** + **Ollama**
- **RAG**: Motor de Retrieval-Augmented Generation para respuestas basadas en pólizas y documentos
- **Documentos**: PDF/Text fragmentados y vectorizados (Spring AI)
- **Backend**: Seguridad (prompt injection), validación metadatos (pólizas), RAG, chat con Ollama

---

## Stack tecnológico

| Componente       | Tecnología |
|------------------|------------|
| Backend          | Java + Spring Boot (REST API) |
| IA / LLM         | Spring AI + Ollama |
| RAG              | Spring AI (embeddings, vector store, retrieval) |
| Procesamiento    | Documentos PDF/Text fragmentados y vectorizados |
| IDE              | Cursor |

---

## Opción recomendada: Monorepo

```
carInsuranceApi/
├── docs/                              # Documentación
│   ├── JERARQUIA_REPOSITORIOS.md
│   ├── API.md
│   ├── ARQUITECTURA.md
│   └── GUARDRAILS.md
│
├── src/main/java/com/example/carInsuranceApi/
│   ├── CarInsuranceApiApplication.java
│   │
│   ├── api/                           # Controladores REST
│   │   ├── ChatController.java        # Endpoint chat → agente
│   │   ├── DocumentController.java    # Carga/gestión documentos
│   │   ├── InsuranceController.java
│   │   └── QuoteController.java
│   │
│   ├── agent/                         # Agente IA (Spring AI)
│   │   ├── AgentService.java          # Orquestación del agente
│   │   └── ChatService.java           # Chat con Ollama
│   │
│   ├── rag/                           # RAG (Spring AI)
│   │   ├── RagService.java            # Retrieval + generación
│   │   ├── RetrievalService.java      # Búsqueda en vector store
│   │   └── EmbeddingService.java      # Vectorización (Spring AI)
│   │
│   ├── document/                      # Procesamiento de documentos
│   │   ├── DocumentService.java       # Ingestion y gestión
│   │   ├── DocumentFragmenter.java    # Fragmentación PDF/Text
│   │   └── policy/
│   │       └── PolicyDocument.java
│   │
│   ├── security/                      # Seguridad (prompt injection)
│   │   ├── PromptInjectionDetector.java
│   │   ├── InjectionPatterns.java
│   │   └── SecurityFilter.java
│   │
│   ├── guardrails/                    # Restricciones del agente
│   │   ├── GuardrailService.java      # Validación de contexto
│   │   ├── ContextValidator.java      # ¿Consulta en ámbito seguros/docs?
│   │   └── ScopeRules.java
│   │
│   ├── config/                        # Configuración
│   │   ├── OllamaConfig.java          # Configuración Ollama
│   │   ├── SpringAiConfig.java        # ChatClient, embeddings
│   │   └── VectorStoreConfig.java     # Vector store (en memoria / PgVector / etc.)
│   │
│   ├── model/                         # DTOs y entidades
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
├── data/                              # Almacenamiento local (opcional)
│   ├── documents/                     # Documentos cargados
│   └── vectors/                       # Vector store (si embebido)
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

## Restricciones del agente

### Guardrails (Backend)

| Regla       | Descripción |
|-------------|-------------|
| Contexto    | Solo responder sobre seguros de autos o contenido de documentos |
| Documentos  | Basar respuestas en pólizas/documentos cargados |
| Fuera de alcance | Rechazar consultas off-topic |

### Seguridad (Backend)

| Amenaza          | Mitigación |
|------------------|------------|
| Prompt injection | `PromptInjectionDetector` antes de enviar a Ollama |

---

## Flujo de peticiones

```
Cliente → [API REST] → [SecurityFilter] → [PolicyMetadataValidator] → [AgentService]
              ↑              ↑                        ↑                      ↓
        HTTP request   Prompt injection         Metadatos pólizas      [RagService]
                                                                             ↓
                                                              Retrieval (vector store)
                                                                             ↓
                                                              [ChatService] → Ollama
                                                                             ↓
Cliente ← [API REST] ←────────────────────────────────────────── Respuesta generada
```

1. **API**: Recibe la petición HTTP
2. **Security**: Detecta prompt injection; si se detecta, se rechaza
3. **Guardrails**: Valida metadatos del usuario (pólizas)
4. **RAG**: Recupera fragmentos relevantes de documentos/pólizas
5. **Agent**: Envía contexto + consulta a Ollama vía Spring AI
6. **API**: Devuelve la respuesta al cliente

---

## Alternativa: Multi-repositorio

```
📁 car-insurance-api    # Spring Boot + Spring AI + Ollama (RAG, agente, documentos)
📁 car-insurance-docs   # Documentación
```
