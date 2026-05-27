---
title: Módulo Cursos
description: Gestión de cursos educativos — CRUD, relación con instituciones/companies, progreso y filtros
---

# Módulo Cursos

El módulo `Course` (`src/domains/course/`) gestiona los cursos educativos asociados a instituciones (companies) e instructores.

## Schema `Course`

Definido en `schemas/course.schema.ts`. Colección: `courses`.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `name` | `string` (requerido) | Nombre del curso |
| `description` | `string` | Descripción del curso |
| `companyId` | `string` (requerido) | ID de la institución/empresa |
| `startDate` | `Date` | Fecha de inicio |
| `endDate` | `Date` | Fecha de fin |
| `status` | `boolean` | Estado activo/inactivo (default: true) |
| `instructorId` | `string` | ID del instructor (User) |
| `maxStudents` | `number` | Cupo máximo de estudiantes |
| `category` | `string` | Categoría del curso |
| `deleted` | `boolean` | Soft delete |

### Índices

```typescript
CourseSchema.index({ companyId: 1 });
CourseSchema.index({ instructorId: 1 });
CourseSchema.index({ status: 1 });
CourseSchema.index({ category: 1 });
```

## Relación con Institutions/Companies

El campo `companyId` referencia a la colección `Company`. El servicio resuelve el nombre de la institución mediante `resolveNames()`, que hace un batch lookup en la colección `Company` trayendo `name`, `nit` y `email`. De igual forma resuelve el instructor desde la colección `User` con `name`, `email` y `documentNumber`.

## Endpoints CRUD

| Método | Ruta | Descripción | Permiso |
|--------|------|-------------|---------|
| `POST` | `/api/courses` | Crear curso (valida fecha inicio < fin) | `10` |
| `GET` | `/api/courses` | Listar cursos (filtros: companyId, status) | Bypass |
| `GET` | `/api/courses/:id` | Obtener curso por ID | — |
| `PUT` | `/api/courses/:id` | Actualizar curso | `10` |
| `DELETE` | `/api/courses/:id` | Soft delete | `10` |

### Endpoints adicionales

| Método | Ruta | Descripción |
|--------|------|-------------|
| `GET` | `/api/courses/list` | Lista simple (solo `_id`, `name`) para selectores |
| `GET` | `/api/courses/progress` | Progreso de cada curso (% de participantes activos) |
| `GET` | `/api/courses/company/:companyId` | Cursos por institución |

### Progreso de cursos (`GET /api/courses/progress`)

Calcula para cada curso activo:
1. Total de participantes inscritos
2. Participantes que han completado al menos una actividad (via `UserResponse`)
3. Porcentaje de avance: `(activeParticipants / totalParticipants) * 100`

Retorna `[{ courseId, courseName, totalParticipants, activeParticipants, progressPercent }]` ordenado por progreso descendente.
