---
name: 4geeks-plan-semanal
description: "Construye un plan semanal accionable con la carga academica actual del estudiante en 4Geeks."
allowed-tools:
  - mcporter
user-invocable: true
---

# 4Geeks - Plan Semanal

Genera un plan semanal realista para mantener el ritmo del curso sin sobrecarga. Esta skill esta optimizada para estudiantes que ya van al dia y quieren sostener consistencia.

## Responsabilidad

Esta skill solo:

- consulta actividades vigentes del estudiante
- organiza la semana por bloques de trabajo
- prioriza continuidad y avance incremental

Esta skill nunca:

- modifica datos de 4Geeks
- marca actividades como completadas
- inventa entregas o calificaciones

## Dependencias

- Skill de autenticacion 4Geeks ya existente en el workspace
- API base: https://4geeks.com/api/v1
- Consultas seguras via `mcporter`
- Puede reutilizar resultados de:
  - 4geeks-obtener-faltantes
  - 4geeks-obtener-trabajo-independiente

## Activacion

Ejemplos:

- "hazme el plan semanal"
- "organiza mi semana de 4geeks"
- "que hago esta semana para mantenerme al dia"

## Flujo

1. Consultar carga academica actual:
   - GET /assignment/me
   - GET /task/me
   - GET /assessment/
2. Filtrar items relevantes de la semana:
   - pendientes o en progreso
   - actividades de practica y proyectos
   - evaluaciones recomendadas
3. Estimar esfuerzo relativo por item (bajo/medio/alto) usando tipo y complejidad del item.
4. Distribuir en 7 dias con foco en equilibrio:
   - bloques cortos en dias de baja energia
   - bloques largos para proyectos en 2-3 dias clave
5. Entregar plan con top 3 prioridades semanales.

## Reglas de planificacion

- Si no hay urgencias criticas, priorizar continuidad sobre intensidad.
- No asignar mas de 1 bloque de alta carga por dia.
- Incluir al menos 1 bloque de repaso/quiz por semana.
- Reservar 1 dia con carga ligera para recuperar energia.

## Salida esperada

1. Resumen semanal de 3-5 lineas:
   - foco de la semana
   - carga estimada
   - objetivo principal
2. Tabla markdown por dia:
   - dia
   - bloque_principal
   - duracion_estimada
   - tipo (proyecto/practica/assessment/repaso)
   - resultado_esperado
3. Seccion "Top 3 de la semana" priorizada.
4. Seccion "Si tengo solo 60 minutos" con opcion minima de avance.

## Manejo de errores

- Si falla una fuente de datos, continuar con las demas y marcar cobertura parcial.
- Si no hay actividades activas, devolver plan de mantenimiento (repaso + assessments).
- Si hay error de autenticacion, informar y sugerir revalidar token.
