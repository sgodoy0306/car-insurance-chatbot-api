# Jerarquía de Repositorios - Car Insurance API & Agente IA

## Visión general del proyecto

- **API**: Backend Spring Boot (REST API) — puerta de entrada HTTP
- **n8n**: RAG, fragmentación de documentos, búsqueda vectorial, agente IA, memoria, herramientas
- **Backend**: Seguridad (prompt injection), validación de metadatos (pólizas), comunicación con n8n vía WebClient

---

## Restricción arquitectónica crítica

> **Esta aplicación Spring Boot NO debe implementar:**
> - Lógica de RAG (Retrieval-Augmented Generation)
> - Fragmentación de documentos
> - Búsqueda vectorial
>
> **Todo eso lo hace n8n.**

### Responsabilidades exclusivas del backend

1. Recibir peticiones HTTP
2. Aplicar seguridad (detección de prompt injection)
3. Validar metadatos del usuario (pólizas)
4. Comunicarse vía HTTP WebClient con los webhooks de n8n

---

## Stack tecnológico

| Componente       | Tecnología |
|------------------|------------|
| Backend          | Java + Spring Boot (REST API) |
| Orquestación IA  | n8n (RAG, fragmentación, vectores, agente, memoria) |
| Comunicación     | HTTP WebClient → webhooks n8n |
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
│   │   ├── ChatController.java        # Endpoint chat → webhook n8n
│   │   ├── InsuranceController.java
│   │   └── QuoteController.java
│   │
│   ├── security/                      # Seguridad (prompt injection)
│   │   ├── PromptInjectionDetector.java
│   │   ├── InjectionPatterns.java
│   │   └── SecurityFilter.java
│   │
│   ├── guardrails/                    # Validación de metadatos
│   │   ├── PolicyMetadataValidator.java   # Validar pólizas del usuario
│   │   └── MetadataRules.java
│   │
│   ├── config/                        # Configuración
│   │   └── N8nConfig.java
│   │
│   ├── client/                        # Cliente HTTP hacia n8n
│   │   └── N8nWebhookClient.java      # WebClient → webhooks n8n
│   │
│   ├── model/                         # DTOs
│   │   ├── request/
│   │   │   ├── ChatRequest.java
│   │   │   └── PolicyMetadata.java
│   │   └── response/
│   │       └── ChatResponse.java
│   │
│   └── exception/
│       ├── InjectionDetectedException.java
│       └── GlobalExceptionHandler.java
│
├── n8n/                               # n8n: RAG, fragmentación, vectores, agente
│   ├── workflows/
│   │   ├── agent-rag-flow.json
│   │   ├── memory-tools.json
│   │   └── document-processing.json   # Fragmentación y vectorización
│   ├── credentials/
│   └── README.md
│
├── docker/
├── scripts/
│   └── import-n8n-workflows.sh
│
├── pom.xml
├── .gitignore
└── README.md
```

---

## Restricciones del agente (implementadas en n8n)

### Guardrails (n8n)

| Regla       | Descripción |
|-------------|-------------|
| Contexto    | Solo responder sobre seguros de autos o contenido de documentos |
| Documentos  | Basar respuestas en pólizas/documentos cargados |

### Seguridad (Backend Spring Boot)

| Amenaza          | Mitigación |
|------------------|------------|
| Prompt injection | `PromptInjectionDetector` antes de enviar a n8n |

---

## Flujo de peticiones

```
Cliente → [API REST] → [SecurityFilter] → [PolicyMetadataValidator] → [N8nWebhookClient]
              ↑              ↑                        ↑                        ↓
        HTTP request   Prompt injection         Metadatos pólizas         WebClient
                                                                              ↓
                                                                          n8n webhook
                                                                              ↓
                                                              (RAG, vectores, agente)
                                                                              ↓
Cliente ← [API REST] ←───────────────────────────────────────────── Respuesta n8n
```

1. **API**: Recibe la petición HTTP
2. **Security**: Detecta prompt injection; si se detecta, se rechaza
3. **Guardrails**: Valida metadatos del usuario (pólizas)
4. **Client**: Envía la petición validada a n8n vía WebClient
5. **n8n**: RAG, fragmentación, vectores, agente → genera la respuesta
6. **API**: Devuelve la respuesta al cliente

---

## Alternativa: Multi-repositorio

```
📁 car-insurance-api    # Spring Boot: HTTP, seguridad, validación metadatos, WebClient
📁 car-insurance-n8n    # n8n: RAG, documentos, vectores, agente
📁 car-insurance-docs   # Documentación
```
