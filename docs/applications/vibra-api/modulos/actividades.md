---
title: Módulo Actividades
description: Gestión de actividades socioemocionales — CRUD, respuestas, actividad diaria, filtros por tipo y juegos gamificados
---

# Módulo Actividades

El módulo `Activities` (`src/domains/activities/`) gestiona las actividades socioemocionales que los participantes realizan en la plataforma Vibra.

## Schema `Activity`

Definido en `schemas/activity.schema.ts`. Colección MongoDB: `activities`.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | `string` (único) | Identificador secuencial auto-generado (`ACT-001`) |
| `title` | `string` (requerido) | Título de la actividad |
| `description` | `string` | Descripción opcional |
| `emotion` | `ObjectId` → `Emotion` | Emoción asociada (ref, indexado) |
| `type` | `enum` | `reto`, `evento_personal`, `actividad_pares`, `otro` (indexado) |
| `difficulty` | `number` (1–5) | Nivel de dificultad (default: 3) |
| `isActive` | `boolean` | Si está activa (default: true, indexado) |
| `resources` | `Array` | Recursos multimedia: `{ type: 'video'\|'audio', url, duration, metadata }` |
| `questions` | `Array` | Preguntas: `{ id, questionText, type: 'multiple'\|'open', options, correctAnswer, points }` |
| `games` | `Array` | Juegos gamificados incrustados (ver abajo) |
| `tips` | `Array` | Tips con emoji, mensaje y categoría |
| `schedule` | `Object` | Programación: `{ date, weekNumber, year }` |
| `createdAt` | `Date` | Fecha de creación |
| `updatedAt` | `Date` | Fecha de actualización |

### Juegos disponibles (`games[]`)

Cada juego se define con `{ type, config, order }`:

- `WordSearch` — Sopa de letras con vocabulario emocional
- `MatchingConcepts` — Emparejamiento de conceptos
- `DiceGame` — Juego de dados con preguntas
- `EmotionBox` — Caja de emociones interactiva

### Índices compuestos

Definidos en el schema:

```
ActivitySchema.index({ type: 1 })
ActivitySchema.index({ isActive: 1 })
ActivitySchema.index({ 'schedule.date': 1 })
```

## Endpoints CRUD

| Método | Ruta | Descripción | Permiso |
|--------|------|-------------|---------|
| `POST` | `/api/activities` | Crear actividad | `16` |
| `GET` | `/api/activities` | Listar activas (paginado, filtro por emoción) | Bypass |
| `GET` | `/api/activities/all` | Listar (paginado, sin filtros) | — |
| `GET` | `/api/activities/:id` | Obtener por ID | — |
| `PUT` | `/api/activities/:id` | Actualizar (incluye `isActive`) | `16` |
| `DELETE` | `/api/activities/:id` | Soft delete (isActive=false) | `16` |

## Submit de respuestas

`POST /api/activities/:id/:userId/submit`

Procesa las respuestas del usuario a las preguntas de una actividad. El servicio `processResponse()`:
1. Busca la actividad y sus preguntas
2. Compara cada respuesta con `correctAnswer`
3. Calcula `totalScore` sumando puntos de respuestas correctas
4. Guarda/actualiza el `UserResponse` en MongoDB
5. Retorna `{ activityId, userId, results, totalScore }`

## Actividad del día

`GET /api/activities/daily/current`

Retorna la actividad activa programada para la fecha actual mediante dos estrategias:
1. **Schedule match** — Busca por `schedule.date` igual al día de hoy usando agregación con `$dateToString`
2. **Fallback** — Si no hay actividad agendada, busca la última creada hoy sin schedule

Respuesta: `{ activity, schedule: { date, status: 'active'\|'no_activity' } }`

## Filtros por tipo

`GET /api/activities/user/:userId?type=reto&page=1&limit=10`

Filtra actividades completadas por un usuario según el tipo:
- `reto` — Actividades tipo reto
- `evento_personal` — Actividades de reflexión personal
- `actividad_pares` — Actividades colaborativas entre pares

El controller expone además:
- `GET /api/activities/count-by-type?type=reto` — Conteo de actividades activas por tipo
- `GET /api/activities/count-all-activities` — Conteo total
- `GET /api/activities/by-month?year=2026&courseId=...` — Actividades completadas agrupadas por mes

## Notificaciones push al crear actividad

Cuando se crea una actividad con `schedule.date` igual al día actual, el servicio envía automáticamente notificaciones push a todos los dispositivos registrados vía Expo Push API.
