---
title: Índices y Optimización
description: Lista de índices creados para optimización de consultas en MongoDB, incluyendo estrategias de soft-delete, auditoría y serial único.
---

# Índices y Optimización

## `activities`

### Filtros por tipo y estado

```json
{ "type": 1, "isActive": 1 }
```

Índice compuesto utilizado en consultas de listado de actividades. Optimiza la búsqueda de actividades por tipo (`type`) combinado con el estado de publicación (`isActive`). Cubre el caso de uso principal del módulo de actividades: obtener todas las actividades activas de un tipo específico.

**Consulta beneficiada:**
```javascript
db.activities.find({ type: "game", isActive: true })
```

---

### Filtro de soft-delete

```json
{ "deleted": 1 }
```

Índice unario para excluir registros eliminados lógicamente en todas las consultas del módulo. Al aplicar soft-delete en la colección, este índice evita escaneos completos al filtrar por `deleted: false`.

---

## `participants`

### Lookup por usuario

```json
{ "userId": 1 }
```

Índice unario sobre la referencia al usuario. Optimiza las búsquedas de participantes por usuario, utilizadas al cargar el perfil de un estudiante, obtener sus cursos inscritos y calcular su progreso general.

**Consulta beneficiada:**
```javascript
db.participants.find({ userId: ObjectId("...") })
```

---

### Lookup por usuario y curso

```json
{ "userId": 1, "courseId": 1 }
```

Índice compuesto que cubre la relación única entre usuario y curso. Previene duplicados a nivel de aplicación y acelera las búsquedas de la inscripción específica de un usuario en un curso determinado.

**Consulta beneficiada:**
```javascript
db.participants.find({ userId: ObjectId("..."), courseId: ObjectId("...") })
```

---

## `feedbacks`

### Ordenamiento descendente por serial

```json
{ "serial": -1 }
```

Índice descendente sobre el campo `serial`. Optimiza las consultas que listan feedbacks ordenados del más reciente al más antiguo, utilizando el serial autoincremental como criterio de ordenación natural.

**Consulta beneficiada:**
```javascript
db.feedbacks.find().sort({ serial: -1 })
```

---

### Filtro de eliminados

```json
{ "deleted": 1 }
```

Índice unario para filtrar feedbacks no eliminados. Crucial para las pantallas de administración donde se listan exclusivamente los registros vigentes.

**Consulta beneficiada:**
```javascript
db.feedbacks.find({ deleted: false })
```

---

## `activitycompletions`

### Agregaciones de puntaje por participante

```json
{ "participantId": 1, "date": -1 }
```

Índice compuesto que soporta las consultas de agregación más pesadas del sistema. Optimiza el cálculo de puntaje acumulado por participante y la obtención del historial de completaciones ordenado por fecha descendente.

**Consulta beneficiada (agregación de puntaje total):**
```javascript
db.activitycompletions.aggregate([
  { $match: { participantId: ObjectId("...") } },
  { $group: { _id: "$participantId", totalScore: { $sum: "$score" } } }
])
```

**Consulta beneficiada (historial):**
```javascript
db.activitycompletions.find({ participantId: ObjectId("...") }).sort({ date: -1 })
```

---

### Filtro de actividad específica

```json
{ "activityId": 1 }
```

Índice unario que acelera las consultas para obtener todas las completaciones de una actividad específica, utilizado en reportes de rendimiento por actividad.

---

## `users`

### Búsqueda por email

```json
{ "email": 1 }
```

Índice único sobre el campo `email` para garantizar la unicidad del correo electrónico y acelerar el proceso de autenticación y búsqueda de usuarios.

---

## Estrategias de optimización

### Soft-delete

Todas las colecciones del sistema implementan soft-delete mediante un campo booleano `deleted`. Esta estrategia permite:

- Recuperación de datos eliminados accidentalmente
- Trazabilidad histórica sin pérdida de información
- Sincronización diferida con réplicas o backups

Cada consulta de listado incluye el filtro `{ deleted: false }`, respaldado por índices unarios en `deleted` para evitar collection scans.

### Campos de auditoría

Todas las colecciones incluyen los campos `createdAt` y `updatedAt`, actualizados automáticamente mediante hooks de Mongoose o triggers de aplicación. Estos campos permiten:

- Ordenar registros por fecha de creación
- Implementar expiración TTL en colecciones temporales
- Auditar cambios y actividad del sistema

### Serial único autoincremental

Las colecciones `emotions`, `feedbacks` y `users` utilizan un campo `serial` numérico único. La estrategia de generación consiste en una colección auxiliar `counters` que mantiene contadores atómicos incrementales:

```javascript
// Colección de contadores
db.counters.findOneAndUpdate(
  { collection: "feedbacks" },
  { $inc: { seq: 1 } },
  { returnDocument: "after", upsert: true }
)
```

Esto garantiza seriales secuenciales sin depender de ObjectId, facilitando la legibilidad en interfaces de usuario y exportaciones.

### Referencias mediante ObjectId

Todas las relaciones entre colecciones utilizan referencias por `ObjectId` en lugar de documentos embebidos. Esto mantiene la normalización de datos y evita los límites de tamaño de documento de MongoDB (16 MB). Las operaciones de lookup se resuelven mediante agregaciones o poblamiento en la capa de aplicación.

### Recomendaciones adicionales

| Estrategia | Descripción |
|---|---|
| **TTL Index** | Considerar un índice TTL sobre `createdAt` en colecciones temporales como `notifications` para expirar registros antiguos automáticamente |
| **Partial Index** | En colecciones con alta cardinalidad de `isActive`, evaluar índices parciales: `{ isActive: { $eq: true } }` |
| **Covered Queries** | Revisar consultas frecuentes para crear índices que cubran todos los campos del filtro y proyección, eliminando la necesidad de acceder al documento |

### Resumen de índices

| Colección | Índice | Uso principal |
|---|---|---|
| `activities` | `{ type: 1, isActive: 1 }` | Filtro por tipo y estado |
| `activities` | `{ deleted: 1 }` | Soft-delete |
| `participants` | `{ userId: 1 }` | Lookup por usuario |
| `participants` | `{ userId: 1, courseId: 1 }` | Inscripción única usuario-curso |
| `feedbacks` | `{ serial: -1 }` | Orden descendente |
| `feedbacks` | `{ deleted: 1 }` | Filtro eliminados |
| `activitycompletions` | `{ participantId: 1, date: -1 }` | Agregaciones de puntaje |
| `activitycompletions` | `{ activityId: 1 }` | Reportes por actividad |
| `users` | `{ email: 1 }` | Autenticación única |
