---
title: Módulo Emociones
description: Catálogo de emociones — schema, endpoints CRUD, distribución y evolución temporal
---

# Módulo Emociones

El módulo `Emotions` (`src/domains/emotions/`) gestiona el catálogo de emociones asociadas a las actividades de Vibra.

## Schema `Emotion`

Definido en `schemas/emotion.schema.ts`. Colección: `emotions`.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `id` | `string` (único) | Identificador (ej. `EMO-001`) |
| `name` | `string` (requerido) | Nombre de la emoción (ej. "Alegría") |
| `description` | `string` | Descripción del estado emocional |
| `icono` | `string` (requerido) | Nombre del archivo de ícono (ej. `joy.png`) |
| `orientationNote` | `string` | Nota de orientación pedagógica |
| `percentNote` | `number` | Porcentaje de referencia (default: 0) |
| `category` | `enum` | `Positiva`, `Negativa`, `Neutra`, `Basica`, `Compleja` |
| `intensity` | `number` (1–10) | Nivel de intensidad |
| `isActive` | `boolean` | Si está activa (default: true) |

## Endpoints

| Método | Ruta | Descripción | Permiso |
|--------|------|-------------|---------|
| `POST` | `/api/emotions` | Crear emoción | `8` |
| `GET` | `/api/emotions` | Listar emociones (paginado) | Bypass |
| `GET` | `/api/emotions/:id` | Obtener por ID | — |
| `PUT` | `/api/emotions/:id` | Actualizar emoción | `8` |
| `DELETE` | `/api/emotions/:id` | Soft delete (isActive=false) | `8` |
| `GET` | `/api/emotions/by-name/:name` | Buscar por nombre exacto | — |
| `GET` | `/api/emotions/distribution` | Distribución de respuestas por emoción | — |
| `GET` | `/api/emotions/evolution` | Evolución temporal (últimos N días) | — |

### Distribución (`/api/emotions/distribution`)

Retorna un arreglo con `{ name, value, icono }` para gráficas de tipo donut/pastel. Acepta filtros:
- `startDate` / `endDate` (ISO string)
- `courseId` — filtrar por curso

### Evolución (`/api/emotions/evolution`)

Retorna `{ date, count }` por día para los últimos N días (default: 30). Útil para gráficas de líneas temporales.

## Relación con Activities

Cada `Activity` tiene una referencia `emotion: ObjectId → Emotion`. El módulo de actividades expone `GET /api/activities/emotions/list` que retorna los IDs de emociones disponibles según las actividades registradas. Esta relación permite:
- Filtrar actividades por emoción
- Asignar actividades según el estado emocional del participante
- Generar gráficas de distribución emocional
