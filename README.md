# Car Insurance API & Agente IA

API de seguros de automóviles que actúa como puerta de entrada hacia un **Agente de IA** orquestado por **n8n**. El backend recibe peticiones HTTP, aplica seguridad y valida metadatos; n8n gestiona RAG, documentos y el agente.

## Restricción arquitectónica crítica

> **Este backend NO implementa RAG, fragmentación de documentos ni búsqueda vectorial.**  
> Todo eso lo realiza n8n. Las únicas responsabilidades del backend son:
> - Recibir peticiones HTTP
> - Detectar prompt injection
> - Validar metadatos del usuario (pólizas)
> - Comunicarse vía WebClient con los webhooks de n8n

## Stack tecnológico

| Componente | Tecnología |
|------------|------------|
| Backend | Java + Spring Boot (REST API) |
| n8n | RAG, fragmentación, vectores, agente IA, memoria |
| Comunicación | HTTP WebClient → webhooks n8n |
| IDE | Cursor |

## Estructura del proyecto

```
carInsuranceApi/
├── docs/                    # Documentación
├── src/main/java/.../
│   ├── api/                 # Controladores REST
│   ├── security/            # Detección prompt injection
│   ├── guardrails/          # Validación metadatos (pólizas)
│   ├── config/              # Configuración n8n
│   ├── client/              # N8nWebhookClient (WebClient)
│   ├── model/               # DTOs
│   └── exception/           # Manejo de errores
├── n8n/                     # RAG, documentos, vectores, agente
├── docker/
└── scripts/
```

## Documentación

- [Jerarquía de repositorios](docs/JERARQUIA_REPOSITORIOS.md)
- [Guardrails y seguridad](docs/GUARDRAILS.md)
