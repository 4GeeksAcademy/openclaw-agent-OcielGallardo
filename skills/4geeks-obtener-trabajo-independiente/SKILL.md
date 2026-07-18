---
name: 4geeks-obtener-trabajo-independiente
description: "Obtiene actividades de trabajo independiente del estudiante y las organiza por prioridad y estado."
allowed-tools:
  - mcporter
user-invocable: true
---

# 4Geeks - Obtener Trabajo Independiente

Recupera y lista el trabajo independiente del estudiante dentro de 4Geeks para que pueda saber que debe avanzar fuera de sesiones en vivo.

## Responsabilidad

Esta skill solo:

- consulta assignments/tasks del estudiante
- filtra actividades de trabajo independiente
- ordena por urgencia y estado

Esta skill nunca:

- marca actividades como completadas
- altera fechas o notas
- envía entregas automaticamente

## Dependencias

- Skill de autenticacion 4Geeks ya existente en el workspace
- API base: https://4geeks.com/api/v1
- Ejecucion de consultas via `mcporter`

## Activacion

Ejemplos:

- "que trabajo independiente tengo"
- "dame mis tareas de estudio autonomo"
- "que debo avanzar por mi cuenta"

## Fuentes de datos

Consultar en este orden:

1. GET /task/me
2. GET /assignment/me
3. Fallback: GET /assignments/task/?user_me=1

## Regla para detectar trabajo independiente

Considerar item como trabajo independiente si cumple cualquiera:

- modalidad/tipo incluye `independent`, `self-paced`, `homework`, `practice`
- no requiere asistencia en vivo (`live_session_required=false` o equivalente)
- pertenece a actividades de practica, ejercicios o proyectos asincronos

Si faltan campos estructurados, aplicar heuristica por titulo/descriptores (`practice`, `exercise`, `proyecto`, `homework`).

## Priorizacion

Ordenar por:

1. vencidas primero
2. por vencer en 72h
3. pendientes sin fecha
4. ya entregadas sin calificar

## Salida esperada

Tabla en markdown con:

- actividad
- tipo
- modulo
- fecha_limite
- estado
- prioridad (`alta`, `media`, `baja`)
- recomendacion_accion

Resumen final:

- total_trabajo_independiente
- vencidas
- por_vencer_72h
- en_progreso
- completadas

## Manejo de errores

- sin autenticacion: pedir revalidar token
- endpoint sin datos: continuar con fallback
- error HTTP: reportar endpoint y codigo
