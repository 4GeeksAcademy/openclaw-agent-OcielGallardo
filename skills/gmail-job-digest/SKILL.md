---
name: gmail-job-digest
description: "Consult Gmail via Composio MCP, identify job-related emails, summarize, and deliver via telegram-messaging."
allowed-tools:
  - mcporter
user-invocable: true
---

# Gmail Job Digest

Consulta Gmail mediante el MCP de Composio, identifica correos relacionados con oportunidades laborales, genera un resumen estructurado y lo envía al usuario mediante la skill `telegram-messaging`.

## Responsabilidad

Esta skill **solo**:

- Accede a Gmail a través del MCP de Composio
- Busca correos relacionados con empleo
- Resume los resultados encontrados
- Envía el resumen mediante `telegram-messaging`

Esta skill **nunca**:

- programa ejecuciones
- responde correos
- modifica, archiva, elimina ni mueve mensajes
- descarga adjuntos
- aplica a ofertas
- usa APIs directas de Gmail

## Dependencias

- [Composio MCP](../README.md) — con Gmail conectado (cuenta `googlecalendar` o `googleworkspace`)
- [`telegram-messaging`](../telegram-messaging/SKILL.md) — para el envío del resumen
- `mcporter` — para invocar las herramientas del MCP de Composio

## Activación

Ejecutar **solo cuando el usuario lo solicite explícitamente**.

Ejemplos de activación:

- *"Revisa mi Gmail"*
- *"Busca ofertas de trabajo"*
- *"Revisa si tengo correos de empleo"*
- *"Haz un resumen de mis correos laborales"*
- *"Comprueba si algún reclutador me escribió"*

Nunca ejecutarse automáticamente.

## Uso del MCP de Composio

Todas las operaciones sobre Gmail deben realizarse **exclusivamente** mediante las herramientas del MCP de Composio.

### Flujo

1. **Descubrir herramientas** — `mcporter call composio.COMPOSIO_SEARCH_TOOLS` con `use_case` relacionado con Gmail
2. **Verificar conexión** — confirmar que la cuenta de Gmail aparece como `"has_active_connection": true`
3. **Seleccionar herramienta** — usar la herramienta adecuada (ej: `GMAIL_FETCH_EMAILS`, `GMAIL_SEARCH_EMAIL`, etc.)
4. **Ejecutar** — mediante `COMPOSIO_MULTI_EXECUTE_TOOL` con la cuenta correcta
5. **Procesar resultados** — filtrar, clasificar, resumir

### Cuenta de Gmail en Composio

Las cuentas de Google en Composio usan el alias `estudio` (id: `googlecalendar_rap-lacca`). Pasar siempre `"account": "googlecalendar_rap-lacca"` en las ejecuciones de herramientas.

## Parámetros opcionales

La skill puede recibir:

| Parámetro | Descripción | Default |
|-----------|-------------|---------|
| `fecha_inicial` | Fecha desde la que buscar (YYYY-MM-DD) | Hoy |
| `etiqueta` | Etiqueta de Gmail (ej: INBOX, UNREAD) | INBOX |
| `carpeta` | Carpeta específica | Inbox |
| `max_resultados` | Número máximo de correos a analizar | 10 |

Si el usuario no proporciona parámetros → revisar solo **correos no leídos** del **Inbox**.

## Identificación de correos laborales

### ✅ Incluir

- Ofertas de empleo
- Invitaciones a entrevistas
- Procesos de selección
- Recruiters / Talent Acquisition
- RRHH / HR
- Confirmaciones de candidatura
- Seguimiento de procesos
- Linkedin messages about jobs

### ❌ Excluir

- Promociones comerciales
- Newsletters generales
- Spam / phishing
- Redes sociales (notificaciones)
- Publicidad
- Recibos y facturas
- Notificaciones ajenas al empleo

## Información a extraer

Para cada correo relevante, obtener cuando sea posible:

| Campo | Descripción |
|-------|-------------|
| Empresa | Nombre de la empresa |
| Remitente | Nombre y email del remitente |
| Asunto | Línea de asunto |
| Fecha | Fecha y hora del correo |
| Categoría | Clasificación (ver abajo) |
| Resumen | 1-3 frases del contenido |
| Acción recomendada | Qué hacer (responder, prepararse, ignorar) |

## Clasificación

Cada correo se clasifica en una de estas categorías:

| Categoría | Descripción |
|-----------|-------------|
| 🆕 Nueva oferta | Oferta de empleo nueva |
| 📩 Invitación a entrevista | Citan para entrevista |
| 📋 Solicitud de información | Piden más datos |
| 🔄 Seguimiento | Alguien hace seguimiento del proceso |
| ❌ Rechazo | Candidatura denegada |
| ℹ️ Actualización del proceso | Cambio de estado en el proceso |
| Otro | No encaja en las anteriores |

## Formato del resumen

```
# 📬 Resumen de empleo

**Fecha:** 2026-07-15
**Correos encontrados:** 3

---

### 🆕 Empresa: Google
**Tipo:** Nueva oferta
**Asunto:** Senior AI Engineer - Google Madrid
**Resumen:** Google busca un Senior AI Engineer para su oficina de Madrid. Requisitos: 5+ años, experiencia en NLP, Python, TensorFlow.
**Acción recomendada:** Revisar requisitos y aplicar antes del 30 de julio.

---

(Si no hay correos relevantes)
> No se encontraron nuevos correos relacionados con oportunidades laborales.
```

## Envío

El resumen se envía **exclusivamente** mediante la skill `telegram-messaging`. No implementar lógica propia de Telegram.

## Manejo de errores

| Escenario | Acción |
|-----------|--------|
| MCP de Composio no disponible | Informar por Telegram |
| Gmail no conectado al MCP | Informar por Telegram |
| Falla de autenticación | Informar por Telegram |
| Error durante la consulta | Informar por Telegram |

## Restricciones

**Nunca:**

- Responder correos
- Modificar correos
- Marcar como leído
- Mover correos
- Eliminar correos
- Descargar adjuntos
- Utilizar APIs distintas del MCP de Composio

## Salida

Siempre producir uno de estos resultados:

1. **Resumen enviado** mediante `telegram-messaging` con los correos clasificados
2. **Mensaje informativo** si no se encontraron correos laborales
3. **Mensaje de error** describiendo el problema encontrado