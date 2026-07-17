# TOOLS.md — Chuleta de José 🩺

Skills definen _cómo_ funcionan las herramientas. Este archivo guarda lo _específico de mi setup_ con Ociel.

---

## MCP / Composio

- **Server:** `composio` vía mcporter (`mcporter list composio`)
- **Transport:** HTTP → `https://connect.composio.dev/mcp`
- **Meta tools (7):**

| Tool | Qué hace |
|------|----------|
| `COMPOSIO_SEARCH_TOOLS` | Descubre tools disponibles por use case. **Siempre llamar primero** cuando se menciona una app externa. |
| `COMPOSIO_GET_TOOL_SCHEMAS` | Obtiene el schema completo de uno o varios tool slugs. |
| `COMPOSIO_MANAGE_CONNECTIONS` | Gestiona conexiones (add / list / rename / remove). |
| `COMPOSIO_WAIT_FOR_CONNECTIONS` | Espera a que el usuario complete el auth de una conexión. |
| `COMPOSIO_MULTI_EXECUTE_TOOL` | Ejecuta hasta 50 tools en paralelo. El core para hacer cosas. |
| `COMPOSIO_REMOTE_BASH_TOOL` | Bash remoto en sandbox (límite 3 min). |
| `COMPOSIO_REMOTE_WORKBENCH` | Jupyter notebook remoto para procesar datos grandes o scripting. |

### Apps conectadas en Composio

#### ✅ Ya usadas

- **Gmail** — `GMAIL_SEND_EMAIL`, `GMAIL_FETCH_EMAILS`, `GMAIL_REPLY_TO_THREAD`, etc.
  - Los mensajes devuelven estructura plana (`data.messages[]`), no anidada.
  - Tema de búsqueda: etiquetas, queries tipo Gmail.
- **Google Calendar** — `GOOGLECALENDAR_CREATE_EVENT`, `GOOGLECALENDAR_LIST_EVENTS`, `GOOGLECALENDAR_FIND_EVENT`, `GOOGLECALENDAR_UPDATE_EVENT`, `GOOGLECALENDAR_DELETE_EVENT`.

#### 🔌 Conectadas — aún no utilizadas

| App | Estado |
|-----|--------|
| **GitHub** | 🔌 Conectada — explorar tool slugs cuando se necesite |
| **Notion** | 🔌 Conectada — explorar tool slugs cuando se necesite |
| **Google Sheets** | 🔌 Conectada — explorar tool slugs cuando se necesite |
| **Google Docs** | 🔌 Conectada — explorar tool slugs cuando se necesite |
| **Linear** | 🔌 Conectada — explorar tool slugs cuando se necesite |
| **Figma** | 🔌 Conectada — explorar tool slugs cuando se necesite |
| **LinkedIn** | 🔌 Conectada — explorar tool slugs cuando se necesite |

> Para explorar tool slugs de cualquiera: `mcporter call composio.COMPOSIO_SEARCH_TOOLS` o via `COMPOSIO_MULTI_EXECUTE_TOOL`.

---

## Skills instaladas

| Skill | Path | Para qué |
|-------|------|----------|
| `telegram-messaging` | `~/.openclaw/workspace/skills/telegram-messaging/SKILL.md` | Enviar mensajes a Telegram, responder en grupos, notificaciones |
| `telegram-status-updates` | `~/.openclaw/workspace/skills/telegram-status-updates/SKILL.md` | Status updates durante ejecución de tareas |
| `gmail-job-digest` | `~/.openclaw/workspace/skills/gmail-job-digest/SKILL.md` | Resumir correos de empleo desde Gmail |

---

## Telegram

- **Chat ID:** `8918853615` (directo)
- **Sender autorizado:** `8918853615`
- **Channel:** `telegram`
- **Account:** `default`
- **Rich messages:** ❌ Deshabilitados para este bot/cuenta
- **Formato:** HTML estándar de Telegram (negritas, enlaces, etc.)

---

## Ociel (el humano)

- **Nombre:** Ociel Gallardo
- **Rol:** Estudiante de Ingeniería en Inteligencia Artificial (Valencia, España)
- **Zona horaria:** `Europe/Madrid` (CEST, UTC+2)
- **Pronombres:** preguntar
- **Email:** ocielgallardo@gmail.com
- **Username TG:** `@GallaTel`

---

## 🌐 Convención @App (Composio + mcporter)

Cuando Ociel escriba `@NombreApp` en un mensaje (ej. `@Linear`, `@Gmail`, `@GitHub`, `@Notion`, `@GoogleCalendar`, `@GoogleSheets`, `@GoogleDocs`, `@Figma`, `@LinkedIn`, etc.), es una señal de que debo:

1. **Usar mcporter** → buscar la meta-tool de Composio correspondiente a esa app.
2. **Ejecutar** lo que pida con ella.

**Flujo automático:**
- Si la app ya está en la tabla de "Apps conectadas" arriba, buscar directamente con `COMPOSIO_SEARCH_TOOLS` los tool slugs si no los tengo memorizados.
- Si no está conectada, avisar antes de seguir.
- Si no especifica tool, preguntar o inferir por contexto.

---
## Notas varias

- **mcporter skill:** `/usr/lib/node_modules/openclaw/skills/mcporter/SKILL.md`
- Cuando se usa Composio, seguir flujo: `COMPOSIO_SEARCH_TOOLS` → revisar plan → `COMPOSIO_GET_TOOL_SCHEMAS` si es necesario → `COMPOSIO_MULTI_EXECUTE_TOOL` (paralelizar tools independientes).
- El archivo `TOOLS.md` se puede perder en reinicios (se almacena en el workspace, que puede resetearse). Si falta, reconstruir con `mcporter list composio --schema --json` para pillar las meta tools.
