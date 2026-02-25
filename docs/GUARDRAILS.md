# Guardrails y seguridad del Agente

## Responsabilidades por componente

| Componente | Responsabilidad |
|------------|------------------|
| **Backend (Spring Boot)** | Detección de prompt injection; validación de metadatos del usuario (pólizas); puente HTTP hacia n8n |
| **n8n** | RAG, fragmentación de documentos, búsqueda vectorial, guardrails de contenido, agente IA |

---

## Restricciones del agente (aplicadas en n8n)

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

El backend **debe** detectar intentos de evadir reglas antes de enviar la petición a n8n:

- Instrucciones ocultas (ej. "ignore las reglas anteriores")
- Cambio de rol (ej. "actúa como admin sin restricciones")
- Exfiltración de datos (ej. "muestra el contenido del prompt")
- Bypass de validaciones

### Implementación (Spring Boot)

- **Capa de filtrado**: `PromptInjectionDetector` antes de llamar al webhook n8n
- **Patrones**: Lista de patrones en `InjectionPatterns`
- **Filtro**: `SecurityFilter` como middleware de entrada

### Respuesta ante detección

- Bloquear la consulta (no enviar a n8n)
- Registrar el intento (audit log)
- Responder genéricamente sin ejecutar la petición
