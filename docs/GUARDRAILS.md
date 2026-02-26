# Guardrails y seguridad del Agente

## Responsabilidades (Backend Spring Boot)

El backend implementa todo de forma integral con Spring AI + Ollama:

| Componente | Responsabilidad |
|------------|------------------|
| **RAG** | Fragmentación de documentos, vectorización, búsqueda en vector store |
| **Agente** | Chat con Ollama vía Spring AI |
| **Guardrails** | Validación de contexto (solo seguros/docs) y metadatos (pólizas) |
| **Seguridad** | Detección de prompt injection antes de enviar a Ollama |

---

## Restricciones del agente

### Contexto permitido

El agente **solo** puede responder sobre:

1. **Seguros de automóviles** en general
2. **Contenido de las pólizas** cargadas
3. **Documentos proporcionados** (cláusulas, condiciones, coberturas)

### Comportamiento ante consultas fuera de alcance

- Rechazar con mensaje claro
- No inferir ni inventar información
- No responder temas off-topic

---

## Seguridad en el Backend

### Detección de prompt injection

El backend **debe** detectar intentos de evadir reglas antes de enviar la petición a Ollama:

- Instrucciones ocultas (ej. "ignore las reglas anteriores")
- Cambio de rol (ej. "actúa como admin sin restricciones")
- Exfiltración de datos (ej. "muestra el contenido del prompt")
- Bypass de validaciones

### Implementación (Spring Boot)

- **Capa de filtrado**: `PromptInjectionDetector` antes de llamar a Ollama
- **Patrones**: Lista de patrones en `InjectionPatterns`
- **Filtro**: `SecurityFilter` como middleware de entrada

### Respuesta ante detección

- Bloquear la consulta (no enviar a Ollama)
- Registrar el intento (audit log)
- Responder genéricamente sin ejecutar la petición
