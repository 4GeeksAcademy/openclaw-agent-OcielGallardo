---
name: 4geeks-obtener-faltantes
description: "Calcula que le falta al estudiante por completar en 4Geeks y lo devuelve como checklist accionable."
allowed-tools:
  - mcporter
user-invocable: true
---

# 4Geeks - Obtener Lo Que Falta Por Completar

Determina exactamente que componentes siguen pendientes en el curso: proyectos, tareas, practicas y evaluaciones.

## Responsabilidad

Esta skill solo:

- cruza datos de assignments, tasks y assessments del usuario
- identifica faltantes reales
- genera checklist priorizado

Esta skill nunca:

- asume completado sin evidencia
- modifica progreso en plataforma

## Dependencias

- Skill de autenticacion 4Geeks ya existente en el workspace
- API base: https://4geeks.com/api/v1
- Consultas via `mcporter`
- Puede reutilizar resultados de:
  - 4geeks-obtener-proyectos
  - 4geeks-obtener-trabajo-independiente

## Activacion

Ejemplos:

- "que me falta por completar"
- "hazme un checklist de pendientes"
- "que debo terminar del curso"

## Flujo

1. Consultar datos del usuario:
   - GET /assignment/me
   - GET /task/me
   - GET /assessment/
2. Detectar estado de cada item:
   - pendiente
   - en progreso
   - entregado sin calificar
   - completado/calificado
3. Seleccionar como faltantes:
   - pendientes
   - en progreso
   - entregados sin calificar (como seguimiento)

## Reglas de faltantes

- Si tiene fecha limite vencida y no completado: marcar `critico`.
- Si vence en <= 72h: marcar `alto`.
- Si no tiene fecha pero es obligatorio: marcar `medio`.
- Si es opcional/enrichment: marcar `bajo`.

## Salida esperada

Checklist markdown por prioridad:

- [ ] item (tipo, modulo, fecha, estado)

Tambien incluir bloques:

- urgentes (critico/alto)
- seguimiento (entregado sin calificar)
- backlog (medio/bajo)

Metricas:

- total_faltantes
- criticos
- altos
- medios
- bajos

## Manejo de errores

- si no hay datos de alguna fuente, continuar con las demas
- si todas las fuentes fallan, reportar fallo global y causa
