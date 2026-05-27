---
title: Módulo Participantes
description: Gestión de participantes — schema con gamificación (puntos, niveles, rachas), relación con usuarios y cursos
---

# Módulo Participantes

El módulo `Participant` (`src/domains/participant/`) gestiona los datos de participación y progreso gamificado de los usuarios en la plataforma Vibra.

## Schema `Participant`

Definido en `schemas/participant.schema.ts`. Colección: `participants`.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `userId` | `ObjectId` → `User` (único) | Referencia al usuario |
| `nickname` | `string` (requerido) | Apodo visible |
| `avatar` | `string` | URL del avatar |
| `points` | `number` | Puntos acumulados (default: 0) |
| `level` | `enum` | Nivel: `bronce`, `plata`, `oro`, `platino`, `diamante` |
| `currentStreak` | `number` | Racha actual de días consecutivos |
| `maxStreak` | `number` | Racha máxima histórica |
| `totalActivitiesCompleted` | `number` | Total de actividades completadas |
| `lastActivityDate` | `Date` | Fecha de última actividad |
| `currentCourse` | `ObjectId` → `Course` | Curso actual del participante |
| `preferences` | `Object` | `{ language, notifications }` |
| `lastSessionDate` | `Date` | Última sesión |
| `isActive` | `boolean` | Estado activo |

### Sistema de niveles (`calculateLevel`)

```typescript
points >= 1000 → 'diamante'
points >= 600  → 'platino'
points >= 300  → 'oro'
points >= 100  → 'plata'
else           → 'bronce'
```

## Relación con Users y Courses

- `userId` → `User`: cada participante es un usuario del sistema (relación 1:1)
- `currentCourse` → `Course`: curso en el que está inscrito el participante

## Endpoints

| Método | Ruta | Descripción | Permiso |
|--------|------|-------------|---------|
| `POST` | `/api/participants` | Crear participante (desde registro User) | — |
| `GET` | `/api/participants` | Listar participantes (paginado) | `9` |
| `GET` | `/api/participants/by-user/:userId` | Obtener por userId | — |
| `GET` | `/api/participants/:id` | Obtener por _id | `9` |
| `POST` | `/api/participants/update` | Actualizar participante | Bypass |
| `POST` | `/api/participants/delete` | Eliminar participante | `9` |
| `PATCH` | `/api/participants/:id/points` | Incrementar puntos + streak + level | — |
| `GET` | `/api/participants/:id/activity-history` | Historial de actividades por día | — |
| `GET` | `/api/participants/leaderboard` | Leaderboard del curso por puntos | — |
| `GET` | `/api/participants/count-all-participants` | Conteo total | Bypass |
| `GET` | `/api/participants/search` | Buscar participantes | `9` |
| `POST` | `/api/participants/bulk` | Carga masiva CSV | `9` |

### Actualización de puntos (`PATCH /:id/points`)

El servicio `updatePoints()` ejecuta:
1. Incrementa `points` según `dto.pointsIncrement`
2. Recalcula `level` vía `calculateLevel()`
3. Si `activityCompleted=true`: incrementa `totalActivitiesCompleted`, actualiza `currentStreak` (día consecutivo, reinicio si >1 día de diferencia), actualiza `maxStreak`
4. Guarda y retorna `{ points, level, currentStreak, maxStreak, totalActivitiesCompleted, lastActivityDate }`

### Activity History (`GET /:id/activity-history`)

Usa agregación sobre `WeeklySchedule` para obtener el historial de los últimos N días con `{ date, count }`, rellenando días sin actividad con count=0.

### Leaderboard (`GET /leaderboard`)

Retorna top N participantes (default: 20) ordenados por puntos descendente, filtrado por `courseId` opcional. Incluye `rank` calculado como número de posición.
