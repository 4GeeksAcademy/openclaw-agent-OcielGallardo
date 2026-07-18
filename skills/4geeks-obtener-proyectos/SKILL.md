---
name: 4geeks-obtener-proyectos
description: "Obtiene proyectos asignados del estudiante en 4Geeks Academy y normaliza su estado en pendiente, entregado o calificado."
allowed-tools:
  - mcporter
user-invocable: true
---

# 4Geeks - Obtener Mis Proyectos

Recupera la lista de proyectos asignados al estudiante desde 4Geeks Academy y clasifica cada proyecto en uno de estos estados:

- pendiente
- entregado
- calificado

## Responsabilidad

Esta skill solo:

- consulta endpoints de 4Geeks para assignments y tasks del usuario autenticado
- detecta los items que representan proyectos
- normaliza estados para presentarlos de forma consistente

Esta skill nunca:

- modifica datos en la plataforma
- entrega proyectos
- recalifica actividades
- crea o elimina recursos

## Dependencias

- Skill de autenticacion 4Geeks ya existente en el workspace (token y headers)
- Base API: https://4geeks.com/api/v1
- Herramientas de consulta HTTP seguras via `mcporter`

## Activacion

Ejecutar cuando el usuario pida, por ejemplo:

- "dime mis proyectos"
- "que proyectos tengo pendientes"
- "muestrame estado de mis proyectos"

## Flujo de consulta

1. Verificar autenticacion activa mediante la skill de login ya existente.
2. Consultar en orden:
   - GET /assignment/me
   - GET /assignments/assignment/?user_me=1 (fallback si el anterior viene vacio)
   - GET /task/me (enriquecimiento opcional)
3. Unificar resultados y extraer solo proyectos.

## Regla para detectar "proyecto"

Marcar como proyecto si se cumple al menos una condicion:

- el tipo/categoria contiene `project`, `proyecto` o `final_project`
- el slug o titulo contiene `project` o `proyecto`
- el assignment_type indica entrega evaluable larga (capstone/final)

Si no hay campo de tipo, usar heuristica por nombre del titulo.

## Normalizacion de estado

Convertir cualquier estado crudo a:

- pendiente:
  - no existe evidencia de entrega (`delivered_at`, `submitted_at`, `delivery_url` vacios)
  - estado crudo: `PENDING`, `TODO`, `NOT_STARTED`, `DRAFT`
- entregado:
  - existe entrega (`delivered_at`, `submitted_at`, `delivery_url`)
  - no hay nota final (`score`, `grade`, `revision_status` final)
- calificado:
  - existe nota o veredicto final (`score`, `grade`, `qualified_at`, `revision_status` en `APPROVED`, `REJECTED`, `GRADED`)

Si hay conflicto entre campos, priorizar evidencias de calificacion sobre entrega y entrega sobre pendiente.

## Salida esperada

Entregar tabla en markdown con columnas:

- proyecto
- modulo (si existe)
- fecha_limite (si existe)
- estado_normalizado
- estado_original
- nota (si existe)
- url_entrega (si existe)

Y agregar resumen:

- total_proyectos
- pendientes
- entregados
- calificados

## Manejo de errores

- Si el token no es valido: informar error de autenticacion y sugerir re-login.
- Si el endpoint falla: reportar endpoint y codigo HTTP.
- Si no hay proyectos: devolver lista vacia con resumen en cero.
