# 🩺 SKILLS_LOG.md — Integración 4Geeks Academy

> Fecha: 2026-07-17
> Agente: José 🩺
> Token de estudiante proporcionado por Ociel

---

## 🧠 Resumen de la conversación

Ociel quería conectarme a su cuenta de **4Geeks Academy** usando su **token de estudiante**, para que pueda:

1. 👀 **Ver su progreso y ejercicios** — pendientes, completados
2. 🤖 **Ayudarle con ejercicios** — leer enunciados y guiarle
3. 📊 **Sacar info de su perfil** — proyectos, entregas, notas

Además, pidió documentar todo el proceso en este archivo.

---

## 🔍 Proceso de descubrimiento del API

### APIs encontradas

| URL | Status | Funciona |
|-----|--------|----------|
| `https://breathecode.herokuapp.com/v1` | ✅ 200 | Sí, pero algunos endpoints 404 |
| `https://4geeks.com/api/v1` | ✅ 200 | Todos los endpoints disponibles |
| `https://api.4geeks.com/v1` | ❌ 404 | No |
| `https://api.breathecode.de/v1` | ❌ 000 | No |

**API principal:** `https://4geeks.com/api/v1`

### Endpoints funcionales (con token de estudiante)

| Endpoint | Descripción |
|----------|-------------|
| `auth/user/me` | Perfil completo del usuario |
| `admissions/academy` | Lista de academias (26) |
| `admissions/academy/{id}` | Detalle de una academia |
| `admissions/me` | Info personal como estudiante |
| `admissions/cohort` / `admissions/cohort/` | Cohorts disponibles |
| `admissions/cohort/me` | Mis cohorts |
| `admissions/student` / `admissions/student/` | Estudiantes |
| `admissions/student/me` | Mi perfil como estudiante |
| `admissions/cohortuser` / `admissions/cohortuser/` | Relación usuario-cohort |
| `assignment` / `assignment/` | Assignments |
| `assignment/me` | Mis assignments |
| `task` / `task/` | Tasks |
| `task/me` | Mis tasks |
| `assignments/assignment` / `assignments/assignment/` | Asignaciones (alternativo) |
| `assignments/task` / `assignments/task/` | Tareas (alternativo) |
| `assessment/` | Assessments / quizzes (69 disponibles) |
| `events/event` / `events/event/` | Eventos |
| `certificate/me` | Mis certificados |
| `mentorship/` | Mentorías |
| `feedback/` | Feedback |
| `notifications/` | Notificaciones |
| `monitor/` | Monitorización |

### Token usado

```
1ba49d1c3a49f46f047ef25aade2b742d5cfb12f
```

**Formato de autenticación:** `Authorization: Token <token>`

---

## 🧪 Datos de Ociel extraídos

**Perfil:**
- Nombre: Ociel Gallardo Estiven
- Email: ociel.galla@gmail.com
- GitHub: @GallaGit
- Academia: 4Geeks Madrid (id=6, slug=madrid-spain)
- Role: student (rol id=21644)
- Se unió: 2026-04-22

**Certificate:**
- "Working with AI Coding Agents" — specialty id=151
- 30 horas de duración
- Estado: PERSISTED (PDF encolado)
- Firmado por: Héctor Chocobar Torrejón (Main Instructor)

**Assessments disponibles (69):**
- Quizzes de Prompt Engineering
- Quizzes de IA y Automatización
- React Native, OTA Updates, Networking...
- (más de 69 assessments)

---

## 🛠️ Skill propuesta: `4geeks-student-api`

### Propósito
Habilitar a José para conectarse al API de 4Geeks Academy, consultar progreso del estudiante, ejercicios, entregas y notas.

### Flujo base
1. Token guardado en `.env` como `GEEKS_TOKEN`
2. URL base: `https://4geeks.com/api/v1`
3. Autenticación: `Authorization: Token {token}`
4. Endpoints principales: `/auth/user/me`, `/assignment/me`, `/task/me`, `/certificate/me`, `/assessment/`

### Comandos previstos
- `@4geeks perfil` — muestra info del estudiante
- `@4geeks progreso` — ejercicios pendientes/completados
- `@4geeks tareas` — tareas y entregas
- `@4geeks ejercicio <id>` — ver enunciado de un ejercicio
- `@4geeks certificados` — certificaciones obtenidas

---

## 🔐 Seguridad

- ✅ Token guardado en `~/.openclaw/.env` (permission `600`)
- ✅ `.env` añadido a `.gitignore`
- ✅ Token nunca subido a git
- ⚠️ El token es de tipo "Token" (no JWT) — no expira a menos que se regenere
- ⚠️ Recomendación: crear un token dedicado para José en 4Geeks.com

---

## 📝 Notas adicionales

- El API retorna arrays directamente (no envueltos en `{results: [...]}`)
- Algunos endpoints requieren filtros específicos (por `?user=21488` o `?user_me=1`)
- La base `breathecode.herokuapp.com/v1` también funciona para auth básico
- El token tiene permisos de estudiante: puede ver su perfil, upload media, telemetry, containers, eventos, mentorías, certificados

---

## 🏗️ Próximos pasos (a decidir con Ociel)

- [ ] Crear skill formal para 4Geeks API
- [ ] Probar `assignment/me` y `task/me` en detalle (pendiente de verificar si devuelven datos vacíos o necesitan filtro)
- [ ] Implementar helpers: `get_profile()`, `get_assignments()`, `get_tasks()`, `get_certificates()`
- [ ] Añadir comando `@4geeks` para interacción rápida

---

## ✅ Implementación realizada (2026-07-18)

### Contexto operativo

Solicité que esta implementación en GitHub Copilot porque el LLM operativo de su agente OpenClaw estaba respondiendo con `TIMEOUT` de forma recurrente durante la construcción de skills.

### Skills creadas en este workspace

Se implementaron 4 skills nuevas en la carpeta `skills/` para mejorar precisión de mapeo/scraping en 4Geeks:

1. `skills/4geeks-obtener-proyectos/SKILL.md`
	- Recupera proyectos asignados
	- Normaliza estado a: `pendiente`, `entregado`, `calificado`
	- Incluye reglas de fallback de endpoints y heurísticas para detectar proyectos

2. `skills/4geeks-obtener-trabajo-independiente/SKILL.md`
	- Lista trabajo independiente del estudiante
	- Ordena por urgencia/prioridad
	- Entrega resumen accionable por actividad

3. `skills/4geeks-obtener-faltantes/SKILL.md`
	- Calcula qué falta por completar
	- Genera checklist por prioridad (`critico`, `alto`, `medio`, `bajo`)
	- Cruza assignments, tasks y assessments

4. `skills/4geeks-resumen-progreso/SKILL.md`
	- Consolida visión global de avance
	- Calcula KPIs y avance porcentual (o estimado con cobertura de datos)
	- Sugiere próximos 3 pasos priorizados

### Nota de integración

Estas skills asumen que la skill de autenticación de 4Geeks ya existente en OpenClaw resuelve token y headers (`Authorization: Token ...`) antes de consultar endpoints.

### Estado

- [x] Skill de proyectos creada
- [x] Skill de trabajo independiente creada
- [x] Skill de faltantes creada
- [x] Skill de resumen de progreso creada
- [x] Documentación actualizada en `SKILLS_LOG.md`