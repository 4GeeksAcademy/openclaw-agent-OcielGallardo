---
name: 4geeks-resumen-progreso
description: "Genera una vista general del avance del curso combinando proyectos, trabajo independiente y faltantes."
allowed-tools:
  - mcporter
user-invocable: true
---

# 4Geeks - Resumen General de Progreso

Construye una vision integral del avance del estudiante en el curso con indicadores claros, porcentaje estimado y proximos pasos.

## Responsabilidad

Esta skill solo:

- consolida datos de progreso academico del estudiante
- calcula metricas de avance comprensibles
- entrega resumen ejecutivo con recomendaciones

Esta skill nunca:

- modifica datos del perfil academico
- inventa metricas sin explicitar supuestos

## Dependencias

- Skill de autenticacion 4Geeks ya existente
- Skills recomendadas para composicion:
  - 4geeks-obtener-proyectos
  - 4geeks-obtener-trabajo-independiente
  - 4geeks-obtener-faltantes
- API base: https://4geeks.com/api/v1

## Activacion

Ejemplos:

- "dame un resumen de mi progreso"
- "cuanto llevo avanzado en el curso"
- "como voy en 4geeks"

## Flujo de consolidacion

1. Obtener perfil y cohort:
   - GET /auth/user/me
   - GET /admissions/student/me
2. Obtener carga academica y estado:
   - resultados de proyectos
   - resultados de trabajo independiente
   - resultados de faltantes
3. Calcular indicadores.

## Indicadores minimos

- proyectos_calificados / total_proyectos
- actividades_completadas / total_actividades
- pendientes_totales
- vencidas_totales
- entregadas_sin_calificar

## Formula sugerida de avance

Si hay datos suficientes, calcular:

avance_porcentual = (completadas / total_actividades) * 100

Donde completadas incluye items con estado final confirmado.

Si los datos son parciales, reportar:

- `avance_porcentual_estimado`
- `cobertura_de_datos` (alta/media/baja)

## Salida esperada

1. Resumen ejecutivo de 4-6 lineas.
2. Tabla de KPI:
   - indicador
   - valor
3. Seccion "Riesgos": vencidas y bloqueos.
4. Seccion "Proximos 3 pasos" accionables.

## Recomendaciones

Las recomendaciones deben ser concretas y priorizadas por impacto:

1. cerrar urgentemente vencidas
2. finalizar entregas cercanas a deadline
3. empujar actividades para subir el porcentaje de avance

## Manejo de errores

- si no hay perfil/cohort, informar falta de contexto academico
- si faltan datos de una skill dependiente, continuar con estimacion parcial
- en error total de API, retornar diagnostico y endpoint fallido
