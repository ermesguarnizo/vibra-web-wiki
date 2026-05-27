---
title: Módulo Feedback
description: Gestión de feedback de usuarios — CRUD, búsqueda, carga CSV y conversión a ideas del backlog
---

# Módulo Feedback

El módulo `Feedback` (`src/domains/feedback/`) gestiona los comentarios, sugerencias y reportes de los usuarios de la plataforma.

## Schema `Feedback`

Definido en `schemas/feedback.schema.ts`. Colección: `feedbacks`.

| Campo | Tipo | Descripción |
|-------|------|-------------|
| `serial` | `string` | Serial auto-generado (`generateSerial`) |
| `title` | `string` (requerido) | Título del feedback |
| `description` | `string` (requerido) | Descripción detallada |
| `isFeature` | `boolean` | Si es una solicitud de funcionalidad |
| `isSupport` | `boolean` | Si es un reporte de soporte |
| `isActive` | `boolean` | Estado activo (default: true) |
| `convertedToIdea` | `boolean` | Si fue convertido a idea del backlog |
| `ideaId` | `string` | ID de la idea generada (ej. `vibra-072`) |
| `deleted` | `boolean` | Soft delete |
| `createdBy` | `string` (requerido) | Usuario creador |
| `event` | `string` | Información del evento |

### Índice

El schema ordena las consultas por `{ serial: -1 }`. No hay índices compuestos explícitos, se usa el `_id` de MongoDB.

## Endpoints CRUD

| Método | Ruta | Descripción |
|--------|------|-------------|
| `GET` | `/api/feedback` | Listar feedbacks (paginado, excluye deleted) |
| `GET` | `/api/feedback/:id` | Obtener por ID |
| `POST` | `/api/feedback` | Crear feedback (asigna serial auto) |
| `PUT` | `/api/feedback/:id` | Actualizar feedback |
| `PUT` | `/api/feedback/:id/status` | Actualizar estado (`isActive`) |
| `DELETE` | `/api/feedback/:id` | Soft delete |
| `DELETE` | `/api/feedback/:id/hard` | Hard delete (eliminación física) |
| `PUT` | `/api/feedback/:id/soft-delete` | Soft delete con parámetros |
| `POST` | `/api/feedback/by-description` | Buscar por descripción exacta |
| `GET` | `/api/feedback/search/term` | Buscar por término (serial, title, description, createdBy) |
| `POST` | `/api/feedback/upload-csv` | Carga masiva desde CSV |

## POST /feedback/:id/convert-to-idea

Endpoint que convierte un feedback en una **idea registrada en el backlog** (archivo `ideas.json` del skill Ideas de OpenCode).

**Flujo:**
1. Busca el feedback por ID
2. Valida que no haya sido convertido previamente
3. Lee el archivo `ideas.json` (ruta configurable via `IDEAS_JSON_PATH` en `.env`)
4. Genera ID auto-incremental (`vibra-{NNN}`)
5. Construye la entrada de idea con `{ id, descripcion, detalle, tags, prioridad, estado, fechas, historial }`
6. Escribe en `ideas.json`
7. Marca el feedback como `convertedToIdea=true` y guarda el `ideaId`

**Body:** `{ title?, description?, priority?, tags[] }`

**Respuesta:** `{ success: true, ideaId: "vibra-072", idea: {...} }`

## Carga CSV

`POST /api/feedback/upload-csv`

Procesa un archivo CSV con columnas `serial, title, description, createdBy` usando la librería `csv-parse`. Inserta en batch y elimina el archivo temporal al finalizar.
