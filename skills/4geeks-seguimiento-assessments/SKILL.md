---
name: 4geeks-seguimiento-assessments
description: "Monitorea assessments/quizzes del estudiante, prioriza que practicar y sugiere proximo intento."
allowed-tools:
  - mcporter
user-invocable: true
---

# 4Geeks - Seguimiento de Assessments

Analiza el estado de assessments y quizzes para detectar oportunidades de mejora academica con recomendaciones concretas.

## Responsabilidad

Esta skill solo:

- consulta assessments disponibles del estudiante
- clasifica por prioridad academica
- sugiere proximo intento o bloque de estudio

Esta skill nunca:

- envia respuestas automaticamente
- altera resultados oficiales
- simula intentos que no existan

## Dependencias

- Skill de autenticacion 4Geeks ya existente en el workspace
- API base: https://4geeks.com/api/v1
- Consultas via `mcporter`
- Integracion opcional con 4geeks-resumen-progreso

## Activacion

Ejemplos:

- "como voy en los assessments"
- "que quizzes debo hacer ahora"
- "dame seguimiento de mis evaluaciones"

## Fuentes de datos

Consultar en este orden:

1. GET /assessment/
2. GET /assignment/me (para contexto de modulo)
3. GET /task/me (para cruzar practica relacionada)

## Clasificacion de prioridad

Asignar prioridad por impacto academico:

- alta:
  - assessment de modulo activo
  - assessment no completado relacionado a proyecto actual
- media:
  - assessment recomendado de refuerzo
  - assessment completado hace tiempo y sujeto a repaso
- baja:
  - assessment opcional o enrichment

## Reglas de recomendacion

- Si hay assessment clave sin completar, sugerirlo como siguiente accion.
- Si ya esta al dia, recomendar 1-2 quizzes de repaso estrategico.
- Si hay muchas opciones, priorizar por modulo actual y relevancia practica.

## Salida esperada

1. Resumen ejecutivo de 3-5 lineas.
2. Tabla markdown:
   - assessment
   - modulo
   - estado
   - prioridad
   - recomendacion
3. Seccion "Siguiente mejor accion" con 1 paso concreto.
4. Seccion "Plan de repaso" con 2 acciones opcionales.

## Manejo de errores

- Si /assessment/ devuelve vacio, informar y continuar con contexto de assignments/tasks.
- Si faltan campos estructurados, usar heuristica por titulo/modulo y marcar confianza media.
- Si hay error de autenticacion, pedir revalidar token.
