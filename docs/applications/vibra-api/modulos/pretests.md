---
title: Módulo Pretests
description: Evaluaciones diagnósticas — schema PreTest, endpoints GET/POST, relación con preguntas y estado de completitud
---

# Módulo Pretests

El módulo `PreTest` (`src/domains/preTest/`) gestiona las evaluaciones diagnósticas (pretests) que los usuarios responden al inicio o durante su proceso en la plataforma.

## Schema `PreTest`

Definido en `schemas/preTest.schema.ts`. Colección: `pretests`.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `testId` | `string` (requerido) | Identificador del test (ej. `PRETEST-BASELINE-EMOTIONS`) |
| `userId` | `string` (requerido) | ID del usuario (documentNumber) |
| `responses` | `Array` | Respuestas: `{ questionId, answer, points }` |
| `totalScore` | `number` | Puntaje total calculado |

## Endpoints

| Método | Ruta | Descripción | Permiso |
|--------|------|-------------|---------|
| `POST` | `/api/pretests` | Crear pretest | `15` |
| `GET` | `/api/pretests` | Listar todos (paginado) | `15` |
| `PATCH` | `/api/pretests/:id` | Actualizar pretest | `15` |
| `DELETE` | `/api/pretests/:id` | Eliminar pretest | `15` |
| `POST` | `/api/pretests/save` | Guardar respuesta de pretest | — |
| `GET` | `/api/pretests/by-test/:testId` | Respuestas de un test específico | `15` |
| `POST` | `/api/pretests/search/userAndTest` | Buscar por userId + testId | `15` |
| `GET` | `/api/pretests/search/user/:userId` | Todos los tests de un usuario | — |
| `GET` | `/api/pretests/result-all` | Todos los resultados | `15` |
| `GET` | `/api/pretests/result/:id` | Resultado por ID | — |
| `GET` | `/api/pretests/status/:userId` | Estado de completitud del usuario | — |
| `GET` | `/api/pretests/count-all-pretests` | Conteo total de pretests | Bypass |

### POST /pretests/save

Endpoint público que guarda las respuestas del pretest. Soporta tanto datos planos como el formato `{ body: "{...}" }` para compatibilidad con clientes existentes.

### GET /pretests/status/:userId

Retorna el estado de todos los tests activos para un usuario: cuáles ha completado, cuáles faltan y si ya puede continuar. El `userId` usado es el `documentNumber` del usuario (no el ObjectId).

## Relación con Questions

Cada respuesta en el array `responses` contiene:
- `questionId` — Identificador de la pregunta (ej. `Q1`)
- `answer` — Respuesta del usuario (puede ser string o cualquier tipo)
- `points` — Puntaje obtenido en esa pregunta

El `totalScore` se calcula sumando los `points` de todas las respuestas. El módulo no define un schema separado para preguntas; las preguntas del pretest se definen a nivel de aplicación cliente y se referencian por `questionId`.
