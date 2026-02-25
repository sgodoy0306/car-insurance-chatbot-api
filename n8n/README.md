# Workflows n8n - Agente IA Seguros de Auto

En este proyecto **n8n** es responsable de:

- **RAG** (Retrieval-Augmented Generation)
- **Fragmentación** de documentos (PDF/Text)
- **Vectorización** y búsqueda vectorial
- **Agente IA**, memoria y herramientas
- **Guardrails** de contenido (respuestas basadas solo en documentos/pólizas)

El backend Spring Boot solo recibe HTTP, valida seguridad y metadatos, y reenvía a n8n vía WebClient.

## Componentes

- **Flujo del agente**: Memoria, herramientas, RAG
- **Procesamiento de documentos**: Fragmentación y vectorización
- **Workflows**: agent-rag-flow, memory-tools, document-processing

## Flujo

1. Cliente → API REST (Spring Boot)
2. API: detección prompt injection + validación metadatos (pólizas)
3. API → WebClient → webhook n8n
4. n8n: RAG, vectores, agente → respuesta
5. API → Cliente
