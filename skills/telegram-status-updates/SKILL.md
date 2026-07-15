---
name: telegram-status-updates
description: "Send Telegram status updates during task execution: start, progress, errors, final result."
allowed-tools:
  - sessions_send
  - sessions_spawn
  - sessions_yield
  - subagents
  - cron
user-invocable: false
---

# Actualización de Estado en Telegram

Permite que un agente de IA envíe actualizaciones de estado a un chat de Telegram durante la ejecución de una tarea, informando el progreso, eventos importantes, errores y el resultado final sin requerir intervención del usuario.

## Dependencia

Requiere la skill [`telegram-messaging`](../telegram-messaging/SKILL.md) y ClawLink para el envío de mensajes. Asegúrate de que Telegram esté conectado antes de usar este procedimiento.

## Contexto

Usar cuando una tarea va a ejecutarse de forma prolongada o en background (sub-agentes, cron jobs, taskflows) y el usuario necesita visibilidad sin estar pendiente.

NO usar para tareas triviales (<30s) que ya ocurren en el chat visible.

## Procedimiento

### 1. Inicio

Identificar el inicio de una tarea relevante. Enviar un mensaje inicial:

```
🐞 Iniciando tarea: [nombre de la tarea]
```

Incluir breve descripción de qué se va a hacer.

### 2. Progreso

Publicar actualizaciones solo cuando ocurra **un cambio significativo de estado**. No en cada paso interno.

**✅ Cuándo enviar:**
- Fase completada (ej: "Búsqueda de documentos lista")
- Hito alcanzado (ej: "Archivos descargados — procesando...")
- Espera externa (ej: "Esperando respuesta de API...")

**❌ Cuándo NO enviar:**
- Cada iteración de un bucle
- Pasos internos que duran segundos
- Cambios triviales

### 3. Errores

Notificar **inmediatamente** si ocurre un error con breve descripción:

```
⚠️ Error en [tarea]: [descripción concisa]
```

Si el error es recuperable, indicar también el plan de reintento:

```
⚠️ Error al conectar con API — reintentando en 30s...
```

Si no es recuperable, indicar que la tarea se detiene.

### 4. Resultado final

Enviar mensaje final con el resultado:

```
✅ [nombre de tarea] completada — [resumen]
```
o
```
❌ [nombre de tarea] falló — [razón]
```

Incluir enlace o referencia al resultado generado cuando corresponda.

## Reglas de limitación (rate-limiting)

- Mínimo **10 segundos** entre mensajes de estado (no flood)
- Si ocurren múltiples cambios en rápida sucesión, agrupar en un solo mensaje
- Máximo **1 mensaje de progreso por minuto** en tareas con muchos pasos
- El mensaje de error y el mensaje final NO están sujetos a estas reglas

## Implementación

Desde un sub-agente o cron job aislado, usar `sessions_send` para enviar los mensajes al chat del usuario.

Ejemplo de envío desde sub-agente:

```javascript
// Enviar mensaje de inicio
sessions_send({
  sessionKey: "agent:main:telegram:direct:8918853615",
  message: "🐞 Iniciando análisis de logs..."
})
```

Cuando el agente principal recibe el mensaje, lo reenvía al chat visible como respuesta normal.

## Notas

- El chat ID del usuario se obtiene del runtime context (`chat_id: "telegram:USER_ID"`)
- Para tareas recurrentes, combinar con `cron` para programar verificaciones periódicas
- Si el usuario no ha interactuado en >1h, considerar si la notificación es urgente antes de enviar
- Respetar silencio nocturno (23:00-08:00 hora del usuario) salvo urgencia real