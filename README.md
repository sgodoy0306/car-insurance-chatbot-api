# Car Insurance API & Agente IA

API de seguros de automóviles con **Agente de IA** implementado mediante **Spring AI** + **Ollama**. Motor RAG para respuestas basadas en pólizas y documentos cargados.

## Stack tecnológico

| Componente | Tecnología |
|------------|------------|
| Backend | Java + Spring Boot (REST API) |
| IA / LLM | Spring AI + Ollama |
| RAG | Spring AI (embeddings, vector store, retrieval) |
| Procesamiento | Documentos PDF/Text fragmentados y vectorizados |
| IDE | Cursor |

## Estructura del proyecto

```
carInsuranceApi/
├── docs/                    # Documentación
├── src/main/java/.../
│   ├── api/                 # Controladores REST
│   ├── agent/               # Agente IA (Spring AI + Ollama)
│   ├── rag/                 # RAG: retrieval, embeddings, vector store
│   ├── document/            # Fragmentación y vectorización de documentos
│   ├── security/            # Detección prompt injection
│   ├── guardrails/          # Validación de contexto y metadatos
│   ├── config/              # Configuración Ollama, Spring AI, vector store
│   ├── model/               # DTOs y entidades
│   └── exception/           # Manejo de errores
├── data/                    # Documentos y vector store (local)
├── docker/                  # API + Ollama
└── scripts/
```

## Restricciones del agente

- **Guardrails**: Solo responder sobre seguros de autos o contenido de documentos
- **Seguridad**: Detección de prompt injection antes de enviar a Ollama

## Documentación

- [Jerarquía de repositorios](docs/JERARQUIA_REPOSITORIOS.md)
- [Guardrails y seguridad](docs/GUARDRAILS.md)
